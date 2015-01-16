## Architecture Overview


* **Command line compiler (tsc):** The batch compilation CLI. Mainly handle reading and writing files for different supported engines (e.g. node js)

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

## Terminology

##### **Full Start/Token Start**

Tokens themselves have what we call a "full start" and a "token start". The "token start" is the more natural version, which is the position in file where the text of a token begins. The "full start" is the point at which the scanner began scanning since the last significant token. When concerned with trivia, we are often more concerned with the full start.

Function | Description
---------|------------
`ts.Node.getStart` | Gets the position in text where the first token of a node started.
`ts.Node.getFullStart` | Gets the position of the "full start" of the first token owned by the node.

#### **Trivia**

Syntax trivia represent the parts of the source text that are largely insignificant for normal understanding of the code, such as whitespace, comments, and even conflict markers.

Because trivia are not part of the normal language syntax (barring ECMAScript ASI rules) and can appear anywhere between any two tokens, they are not included in the syntax tree. Yet, because they are important when implementing a feature like refactoring and to maintain full fidelity with the source text, they are still accessible through our APIs on demand.

Because the `EndOfFileToken` can have nothing following it (neither token nor trivia), all trivia naturally precedes some non-trivia token, and resides between that token's "full start" and the "token start"

It is a convenient notion to state that a comment "belongs" to a `Node` in a more natural manner though. For instance, it might be visually clear that the `genie` function declaration owns the last two comments in the following example:

```TypeScript
var x = 10; // This is x.

/**
 * Postcondition: Grants all three wishes.
 */
function genie([wish1, wish2, wish3]: [Wish, Wish, Wish]) {
    while (true) {
    }
} // End function
```

This is despite the fact that the function declaration's full start occurs directly after `var x = 10;`.

We follow [Roslyn's notion of trivia ownership](https://github.com/dotnet/roslyn/wiki/Roslyn%20Overview#syntax-trivia) for comment ownership. In general, a token owns any trivia after it on the same line up to the next token. Any comment after that line is associated with the following token. The first token in the source file gets all the initial trivia, and the last sequence of trivia in the file is tacked onto the end-of-file token, which otherwise has zero width.

For most basic uses, comments are the "interesting" trivia. The comments that belong to a Node which can be fetched through the following functions:

Function | Description
---------|------------
`ts.getLeadingCommentRanges` | Returns ranges of comments between the first line break following the given position and the token itself (probably most useful with `ts.Node.getFullStart`).
`ts.getTrailingCommentRanges` | Returns ranges of comments until the first line break following the given position (probably most useful with `ts.Node.getEnd`).

In the event that you are concerned with richer information of the token stream, `createScanner` also has a `skipTrivia` flag which you can set to `false`, and use `setText`/`setTextPos` to scan at different points in a file.

## Using the compiler API

### Getting set up

First you'll need to install TypeScript >=1.4 from `npm`.

Once that's done, you'll need to link it from wherever your project resides. If you don't link from within a Node project, it will just link globally.

```
npm install -g typescript
npm link typescript
```

Once that's done, [just grab our definitions file](https://github.com/Microsoft/TypeScript/blob/release-1.4/bin/typescript.d.ts) and include it from your TypeScript file.

That's it, you're ready to go. Now you can try out some of the following examples.

### A minimal compiler

Let's try to write a barebones compiler that can compile a TypeScript string to its corresponding JavaScript. We will need to create a `Program`. This is as simple as calling `createProgram`. `createProgram` abstracts any interaction with the underlying system in the `CompilerHost` interface. The `CompilerHost` allows the compiler to read and write files, get the current directory, ensure that files and directories exist, and query some of the underlying system properties such as case sensitivity and new line characters. For convenience, we expose a function to create a default host using `createCompilerHost`.

```TypeScript
/// <reference path="typings/node/node.d.ts" />
/// <reference path="typings/typescript/typescript.d.ts" />

import ts = require("typescript");

export function compile(filenames: string[], options: ts.CompilerOptions): void {
    var host = ts.createCompilerHost(options);
    var program = ts.createProgram(filenames, options, host);
    var checker = ts.createTypeChecker(program, /*produceDiagnostics*/ true);
    var result = checker.emitFiles();

    var allDiagnostics = program.getDiagnostics()
        .concat(checker.getDiagnostics())
        .concat(result.diagnostics);

    allDiagnostics.forEach(diagnostic => {
        var lineChar = diagnostic.file.getLineAndCharacterFromPosition(diagnostic.start);
        console.log(`${diagnostic.file.filename} (${lineChar.line},${lineChar.character}): ${diagnostic.messageText}`);
    });

    console.log(`Process exited with code '${result.emitResultStatus}'.`);
}

compile(process.argv.slice(2), { noImplicitAny: true, noEmitOnError: true,
                                 target: ts.ScriptTarget.ES5, module: ts.ModuleKind.CommonJS});
```

### A simple transform function

Creating a compiler is simple enough, but you may not want to actually do traditional reads and writes from the file system; for instance, you may have a text buffer for your TypeScript input, and you may want to send/store the resulting JavaScript as JSON. What's more, you may want to use/modify the resulting JavaScript in some way. In such a case, you will need to provide your own `CompilerHost`. 

```TypeScript
/// <reference path="typings/node/node.d.ts" />
/// <reference path="typings/typescript/typescript.d.ts" />

import ts = require("typescript");
import fs = require("fs");
import path = require("path");

function transform(contents: string, libSource: string, compilerOptions: ts.CompilerOptions = {}) {
    // Generated outputs
    var outputs = [];
    // Create a compilerHost object to allow the compiler to read and write files
    var compilerHost = {
        getSourceFile: function (filename, languageVersion) {
            if (filename === "file.ts")
                return ts.createSourceFile(filename, contents, compilerOptions.target, "0");
            if (filename === "lib.d.ts")
                return ts.createSourceFile(filename, libSource, compilerOptions.target, "0");
            return undefined;
        },
        writeFile: function (name, text, writeByteOrderMark) {
            outputs.push({ name: name, text: text, writeByteOrderMark: writeByteOrderMark });
        },
        getDefaultLibFilename: function () { return "lib.d.ts"; },
        useCaseSensitiveFileNames: function () { return false; },
        getCanonicalFileName: function (filename) { return filename; },
        getCurrentDirectory: function () { return ""; },
        getNewLine: function () { return "\n"; }
    };
    // Create a program from inputs
    var program = ts.createProgram(["file.ts"], compilerOptions, compilerHost);
    // Query for early errors
    var errors = program.getDiagnostics();
    // Do not generate code in the presence of early errors
    if (!errors.length) {
        // Type check and get semantic errors
        var checker = program.getTypeChecker(true);
        errors = checker.getDiagnostics();
        // Generate output
        checker.emitFiles();
    }
    return {
        outputs: outputs,
        errors: errors.map(function (e) { return e.file.filename + "(" + e.file.getLineAndCharacterFromPosition(e.start).line + "): " + e.messageText; })
    };
}

// Calling our transform function using a simple TypeScript variable declarations, 
// and loading the default library like:
var source = "var x: number  = 'string'";
var libSource = fs.readFileSync(path.join(path.dirname(require.resolve('typescript')), 'lib.d.ts')).toString();
var result = transform(source, libSource);

console.log(JSON.stringify(result));
```

will generate the following output:

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

### Traversing the AST with a little linter

As mentioned above, the `Node` interface is the root of our AST. Generally, we use the `forEachChild` function in a recursive manner to traverse. This subsumes the visitor pattern and often gives more flexibility.

As an example of how one could traverse the AST, consider a minimal linter that does the following:

* Checks that all looping construct bodies are enclosed by curly braces.
* Checks that all if/else bodies are enclosed by curly braces.
* The "stricter" equality operators (`===`/`!==`) are used instead of the "loose" ones (`==`/`!=`).

```TypeScript
/// <reference path="typings/node/node.d.ts" />
/// <reference path="typings/typescript/typescript.d.ts" />

import ts = require("typescript");

export function delint(sourceFile: ts.SourceFile) {
    delintNode(sourceFile);

    function delintNode(node: ts.Node) {
        switch (node.kind) {
            case ts.SyntaxKind.ForStatement:
            case ts.SyntaxKind.ForInStatement:
            case ts.SyntaxKind.WhileStatement:
            case ts.SyntaxKind.DoStatement:
                if ((<ts.IterationStatement>node).statement.kind !== ts.SyntaxKind.Block) {
                    report(node, "A looping statement's contents should be wrapped in a block body.");
                }
                break;
            case ts.SyntaxKind.IfStatement:
                var ifStatement = (<ts.IfStatement>node);
                if (ifStatement.thenStatement.kind !== ts.SyntaxKind.Block) {
                    report(ifStatement.thenStatement, "An if statement's contents should be wrapped in a block body.");
                }
                if (ifStatement.elseStatement &&
                    ifStatement.elseStatement.kind !== ts.SyntaxKind.Block && ifStatement.elseStatement.kind !== ts.SyntaxKind.IfStatement) {
                    report(ifStatement.elseStatement, "An else statement's contents should be wrapped in a block body.");
                }
                break;

            case ts.SyntaxKind.BinaryExpression:
                var op = (<ts.BinaryExpression>node).operator;

                if (op === ts.SyntaxKind.EqualsEqualsToken || op === ts.SyntaxKind.ExclamationEqualsToken) {
                    report(node, "Use '===' and '!=='.")
                }
                break;
        }

        ts.forEachChild(node, delintNode);
    }

    function report(node: ts.Node, message: string) {
        var lineChar = sourceFile.getLineAndCharacterFromPosition(node.getStart());
        console.log(`${sourceFile.filename} (${lineChar.line},${lineChar.character}): ${message}`)
    }
}

var fileNames = process.argv.slice(2);
var options: ts.CompilerOptions = { target: ts.ScriptTarget.ES6, module: ts.ModuleKind.AMD };
var host = ts.createCompilerHost(options);
var program = ts.createProgram(fileNames, options, host);

program.getSourceFiles().forEach(delint);
```

In this example, we did not need to create a type checker because all we wanted to do was traverse each `SourceFile`.

### Incremental build support using the language services

The services layer provide a set of additional set of utilities that can help simplify some complex scenarios. In the snippet below, we will try to build an incremental build server that watches a set of files and update the only the outputs of the file that changed.
We will achieve this through creating a LanguageService object. Similar to the program in the previous example, we need a LanguageServiceHost. The LanguageServiceHost augments the concept of a file with a version, isOpen flag, and a ScriptSnapshot. Version, allows the language service to track changes to files. isOpen, tells the language service to keep AST in memory as the file is in use. ScriptSnapshot is an abstraction over text that allows the language service to query for changes.

```TypeScript
/// <reference path="typings/node/node.d.ts" />
/// <reference path="typings/typescript/typescript.d.ts" />

import fs = require("fs");
import ts = require("typescript");
import path = require("path");


function watch(filenames: string[], options: ts.CompilerOptions) {
    var files: ts.Map<{ version: number; text: string; }> = {};
    
    // Add the default library file
    filenames.unshift(path.join(path.dirname(require.resolve('typescript')), 'lib.d.ts'));

    // initialize the list of files
    filenames.forEach(filename => {
        files[filename] = { version: 0, text: fs.readFileSync(filename).toString() };
    });

    // Create the language service host to allow the LS to communicate with the host
    var servicesHost: ts.LanguageServiceHost = {
        getScriptFileNames: () => filenames,
        getScriptVersion: (filename) => files[filename] && files[filename].version.toString(),
        getScriptSnapshot: (filename) => {
            var file = files[filename];
            return {
                getText: (start, end) => file.text.substring(start, end),
                getLength: () => file.text.length,
                getLineStartPositions: () => [],
                getChangeRange: (oldSnapshot) => undefined
            };
        },
        getCurrentDirectory: () => process.cwd(),
        getScriptIsOpen: () => true,
        getCompilationSettings: () => options,
        getDefaultLibFilename:(options) => 'lib.d.ts',
        log: (message) => console.log(message)
    };

    // Create the language service files
    var services = ts.createLanguageService(servicesHost, ts.createDocumentRegistry())

    // Now let's watch the files
    filenames.forEach(filename => {
        // First time around, emit all files
        emitFile(filename);

        // Add a watch on the file to handle next change
        fs.watchFile(filename,
            { persistent: true, interval: 250 },
            (curr, prev) => {
                // Check timestamp
                if (+curr.mtime <= +prev.mtime) {
                    return;
                }

                var file = files[filename];

                // Update the version to signal a change in the file
                file.version++;

                // Clear the text to force a new read
                file.text = fs.readFileSync(filename).toString();

                // write the changes to disk
                emitFile(filename);
            });
    });


    function emitFile(filename: string) {
        var output = services.getEmitOutput(filename);

        if (output.emitOutputStatus === ts.EmitReturnStatus.Succeeded) {
            console.log(`Emitting ${filename}`);
        }
        else {
            console.log(`Emitting ${filename} failed`);
            var allDiagnostics = services.getCompilerOptionsDiagnostics()
                .concat(services.getSyntacticDiagnostics(filename))
                .concat(services.getSemanticDiagnostics(filename));

            allDiagnostics.forEach(diagnostic => {
                var lineChar = diagnostic.file.getLineAndCharacterFromPosition(diagnostic.start);
                console.log(`  ${diagnostic.file && diagnostic.file.filename} (${lineChar.line},${lineChar.character}): ${diagnostic.messageText}`);
            });
        }

        output.outputFiles.forEach(o => {
            fs.writeFileSync(o.name, o.text, "utf8");
        });
    }
}

// Initialize files constituting the program as all .ts files in the current directory
var currentDirectoryFiles = fs.readdirSync(process.cwd()).
    filter(filename=> filename.length >= 3 && filename.substr(filename.length - 3, 3) === ".ts");
    //map(filename => path.join(process.cwd(), filename));

// Start the watcher
watch(currentDirectoryFiles, { target: ts.ScriptTarget.ES5, module: ts.ModuleKind.CommonJS });
```