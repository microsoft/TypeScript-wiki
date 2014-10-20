## Architecture Overview


* **Command line compiler (tsc):** The batch compilation CLI. Mainly handel reading and writing files for different supported engines (e.g. node js)

* **Parser:** Starting from a set of sources, and following the productions of the language grammar, to generate an Abstract Syntax Tree (AST)

* **Binder:** Linking declarations contributing to the same structure using a Symbol (e.g. different declarations of the same interface or module, or a function and a module with the same name). This allows the type system to reason about these named declarations. 

* **Type resolver/ Checker:** Resolving types of each construct, checking semantic operations and generate diagnostics as appropriate.

* **Emitter:** Output generated from a set of inputs (.ts and .d.ts) files can be one of: JavaScript (.js), definitions (.d.ts), or source maps (.js.map)

* **Services:** Provides an additional layer of language services on top of the basic output generation supported by the CLI. The services layer functionalities are ideal for an IDE but can be used for a variety of ways other than IDE scenarios. The services layer also override the basic compiler data structures allowing for a richer tree API.

* **Pre-processor:** The "Compilation Context" refers to all files involved in a "program". The context is created by inspecting all files passed in to the compiler on the command line, in order, and then adding any files they may reference directory or indirectly through "import" statements and /// <references > tags.
The result of walking the reference graph is an ordered list of source files, that constitute the program.
When resolving imports, preference is given to ".ts" files over ".d.ts" files to ensure the most up-to-date files are processed.
The compiler does a node-like process to resolve imports by walking up the directory chain to find a source file with a .ts or .d.ts extension matching the requested import.
Failed import resolution does not result in an error, as an ambient module could be already declared.

## Data Structures

* **Node:** The basic building block of the Abstract Syntax Tree (AST). In general node represent non-terminals in the language grammar; some terminals are kept in the tree such as identifiers and literals.

* **SourceFile:** The AST of a given source file. A SourceFile is itself a Node; it provides an additional set of interfaces to access the raw text of the file, references in the file, the list of identifiers in the file, and mapping from a position in the file to a line and character numbers.

* **Program:** A collection of SourceFiles and a set of compilation options that represent a compilation unit. The program is the main entry point to the type system and code generation. 

* **Symbol:** A named declaration. Symbols are created as a result of binding. Symbols connect declarations nodes in the tree to other declarations contributing to the same entity. Symbols are the basic building block of the semantic system. 

* **Type:** Types is the other part of the semantic system. Types can be named (e.g. classes and interfaces), or anonymous (e.g. object types). 

* **Signature:** There are thee types of signatures in the language, call, construct and index signatures.

## Using the compiler API

### A simple transform function

Let's try to write a transform function that can compile a TypeScript string to its corresponding JavaScript. We will need to create a "Program" to wrap our string. To create a program we will call ```createProgram```. createProgram abstracts any interaction with the underlying system in the "CompilerHost" interface. The CompilerHost allows the compiler to read and write files, get the current directory, ensure that files and directories exist, and query some of the underlying system properties such as case sensitivity and new line characters. For the purposes of our transform function, we will stub out most of these functions. Two functions that we will override are **readFile** and **writeFile**. The compiler calls into **readFile** to get the AST of a given input file; since we only have two files: the string to transform and the default library (lib.d.ts) we can hard code most of this section. **wrieFile** will capture the output in a list, and this, along with the errors, will be the output of our transform function.

```TypeScript
function transform(contents: string, libSource: string, compilerOptions: ts.CompilerOptions = { }) {
    // Generated outputs
    var outputs: ts.OutputFile[] = [];

    // Create a compilerHost object to allow the compiler to read and write files
    var compilerHost: ts.CompilerHost = {
        getSourceFile: (filename, languageVersion) => {
            if (filename === "file.ts") 
				return ts.createSourceFile(filename, contents, compilerOptions.target, /*version:*/ "0");
            if (filename === "lib.d.ts") 
				return ts.createSourceFile(filename, libSource, compilerOptions.target, /*version:*/ "0");
            return undefined;
        },
        writeFile: (name, text, writeByteOrderMark) => { 
			outputs.push({ name: name, text: text, writeByteOrderMark: writeByteOrderMark });
		},
        getDefaultLibFilename: () => "lib.d.ts",
        useCaseSensitiveFileNames: () => false,
        getCanonicalFileName: (filename) => filename,
        getCurrentDirectory: (): string => undefined,
        getNewLine: () => "\n"
    };

    // Create a program from inputs
    var program = ts.createProgram(["file.ts"], compilerOptions, compilerHost);

    // Query for early errors
    var errors = program.getDiagnostics();

    // Do not generate code in the presence of early errors
    if (!errors.length) {
        // Type check and get semanic errors
        var checker = program.getTypeChecker(/*fullTypeCheckMode*/ true);
        errors = checker.getDiagnostics();

        // Generate output
        checker.emitFiles();
    }

    return {
        outputs: outputs,
        errors: ts.map(errors, e => 
			e.file.filename + "(" + e.file.getLineAndCharacterFromPosition(e.start).line + "): "  + e.messageText)
    };
}
```

Calling our transform function using a simple TypeScript variable declarations, and loading the default library like:

```TypeScript
var fs = require("fs");
var soruce = "var x: number  = 'string'";
var libSoruces = fs.readFileSync("lib.d.ts");
var result = transform(soruce, libSoruces);

console.log(JSON.stringify(result));

```

Generates the following output:

```JSON
{
    "outputs": [
        {
            "name": "file.js",
            "text": "var x = 'string';\n"
        }
    ],
    "errors": [
        "file.ts(1): Type 'string' is not assignable to type 'number'."
    ]
}
```


### Incremental build support using the language services

The services layer provide a set of additional set of utilities that can help simplify some complex scenarios. In the snippet below, we will try to build an incremental build server that watches a set of files and update the only the outputs of the file that changed.
We will achieve this through creating a LanguageService object. Similar to the program in the previous example, we need a LanguageServiceHost. The LanguageServiceHost augments the concept of a file with a version, isOpen flag, and a ScriptSnapshot. Version, allows the language service to track changes to files. isOpen, tells the language service to keep AST in memory as the file is in use. ScriptSnapshot is an abstraction over text that allows the language service to query for changes.

```TypeScript
var fs = require("fs");

// Files constituting our program
var files =  [
    { filename: "file1.ts", version: 0, text: undefined },
    { filename: "file2.ts", version: 0, text: undefined }
];

// Create the language service host to allow the LS to communicate with the host
var servicesHost: ts.LanguageServiceHost = {
    getScriptFileNames: () => ts.map(files, f => f.filename),
    getScriptVersion: (filename) => ts.forEach(files, 
            f => f.filename === filename ? f.version.toString() : undefined),
    getScriptSnapshot: (filename) => {
        var file = ts.forEach(files, f => f.filename === filename ? f : undefined);
        // Read the text if we have not read it already
        var readText = () => file.text ? 
            file.text : file.text = fs.readFileSync(filename).toString();
        return {
            getText: (start, end) => readText().substring(start, end),
            getLength: () => readText().length,
            getLineStartPositions: () => [],
            getChangeRange: (oldSnapshot) => undefined
        };
    },
    log: (message) => console.log(message),
    getCurrentDirectory: () => undefined,
    getScriptIsOpen: () => true,
    getDefaultLibFilename: () => "lib.d.ts",
    getLocalizedDiagnosticMessages: () => undefined,
    getCancellationToken: () => undefined,
    getCompilationSettings: () => { return {}; },
};

// Create the language service files
var services = ts.createLanguageService(servicesHost, ts.createDocumentRegistry())

// Write a single file outputs
var emitFile = (filename: string) => {
    var output = services.getEmitOutput(filename);
    ts.forEach(output.outputFiles, o => {
        console.log("Writing file: " + o.name);
        fs.writeFileSync(o.name, o.text, "utf8");
    });
};

// Now let's watch the files
ts.forEach(files, f => {
    // First time around, emit all files
    emitFile(f.filename);

    // Add a watch on the file to handle next change
    fs.watchFile(f.filename, 
        { persistent: true, interval: 250 }, 
        (curr, prev) => {
            // Check timestamp
            if (+curr.mtime <= +prev.mtime) {
                return;
            }

            // Update the version to signal a change in the file
            f.version++;

            // Clear the text to force a new read
            f.text = undefined;

            // write the changes to disk
            emitFile(f.filename);
        });
});
```