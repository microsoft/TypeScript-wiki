> ##*Disclaimer*
Keep in mind that this is not yet a stable API - we’re releasing this as version 0.5, and things will be changing over time. As a first iteration, there will be a few rough edges. We encourage any and all feedback from the community to improve the API. To allow users to transition between future releases, we will be documenting any [[API Breaking Changes]] per new release.

## Getting set up

First you'll need to install TypeScript >=1.6 from `npm`.

> ##### For API Samples compatible with **TypeScript == 1.4** please see [[Using the Compiler API (TypeScript 1.4)]]

Once that's done, you'll need to link it from wherever your project resides. If you don't link from within a Node project, it will just link globally.

```
npm install -g typescript
npm link typescript
```

That's it, you're ready to go. Now you can try out some of the following examples.

## A minimal compiler

Let's try to write a barebones compiler that will take a list of TypeScript files and compile down to their corresponding JavaScript. We will need to create a `Program`. This is as simple as calling `createProgram`. `createProgram` abstracts any interaction with the underlying system in the `CompilerHost` interface. The `CompilerHost` allows the compiler to read and write files, get the current directory, ensure that files and directories exist, and query some of the underlying system properties such as case sensitivity and new line characters. For convenience, we expose a function to create a default host using `createCompilerHost`.

```TypeScript
/// <reference path="typings/node/node.d.ts" />

import * as ts from "typescript";

function compile(fileNames: string[], options: ts.CompilerOptions): void {
    let program = ts.createProgram(fileNames, options);
    let emitResult = program.emit();

    let allDiagnostics = ts.getPreEmitDiagnostics(program).concat(emitResult.diagnostics);

    allDiagnostics.forEach(diagnostic => {
        let { line, character } = diagnostic.file.getLineAndCharacterOfPosition(diagnostic.start);
        let message = ts.flattenDiagnosticMessageText(diagnostic.messageText, '\n');
        console.log(`${diagnostic.file.fileName} (${line + 1},${character + 1}): ${message}`);
    });

    let exitCode = emitResult.emitSkipped ? 1 : 0;
    console.log(`Process exiting with code '${exitCode}'.`);
    process.exit(exitCode);
}

compile(process.argv.slice(2), {
    noEmitOnError: true, noImplicitAny: true,
    target: ts.ScriptTarget.ES5, module: ts.ModuleKind.CommonJS
});
```

## A simple transform function

Creating a compiler is simple enough, but you may want to just get the corresponding JavaScript output given TypeScript sources. For this you can use ts.transpileModule to get a string => string transformation in two lines.

```TypeScript
import * as ts from "typescript";

const source = "let x: string  = 'string'";

let result = ts.transpileModule(source, { compilerOptions: { module: ts.ModuleKind.CommonJS } });

console.log(JSON.stringify(result));
```

## Traversing the AST with a little linter

As mentioned above, the `Node` interface is the root of our AST. Generally, we use the `forEachChild` function in a recursive manner to traverse. This subsumes the visitor pattern and often gives more flexibility.

As an example of how one could traverse the AST, consider a minimal linter that does the following:

* Checks that all looping construct bodies are enclosed by curly braces.
* Checks that all if/else bodies are enclosed by curly braces.
* The "stricter" equality operators (`===`/`!==`) are used instead of the "loose" ones (`==`/`!=`).

```TypeScript
/// <reference path="typings/node/node.d.ts" />

import {readFileSync} from "fs";
import * as ts from "typescript";

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
                let ifStatement = (<ts.IfStatement>node);
                if (ifStatement.thenStatement.kind !== ts.SyntaxKind.Block) {
                    report(ifStatement.thenStatement, "An if statement's contents should be wrapped in a block body.");
                }
                if (ifStatement.elseStatement &&
                    ifStatement.elseStatement.kind !== ts.SyntaxKind.Block &&
                    ifStatement.elseStatement.kind !== ts.SyntaxKind.IfStatement) {
                    report(ifStatement.elseStatement, "An else statement's contents should be wrapped in a block body.");
                }
                break;

            case ts.SyntaxKind.BinaryExpression:
                let op = (<ts.BinaryExpression>node).operatorToken.kind;
                if (op === ts.SyntaxKind.EqualsEqualsToken || op == ts.SyntaxKind.ExclamationEqualsToken) {
                    report(node, "Use '===' and '!=='.")
                }
                break;
        }

        ts.forEachChild(node, delintNode);
    }

    function report(node: ts.Node, message: string) {
        let { line, character } = sourceFile.getLineAndCharacterOfPosition(node.getStart());
        console.log(`${sourceFile.fileName} (${line + 1},${character + 1}): ${message}`);
    }
}

const fileNames = process.argv.slice(2);
fileNames.forEach(fileName => {
    // Parse a file
    let sourceFile = ts.createSourceFile(fileName, readFileSync(fileName).toString(), ts.ScriptTarget.ES6, /*setParentNodes */ true);

    // delint it
    delint(sourceFile);
});
```

In this example, we did not need to create a type checker because all we wanted to do was traverse each `SourceFile`.

## Incremental build support using the language services

> Please refer to the [[Using the Language Service API]] page for more details.

The services layer provide a set of additional utilities that can help simplify some complex scenarios. In the snippet below, we will try to build an incremental build server that watches a set of files and updates only the outputs of the files that changed.
We will achieve this through creating a LanguageService object. Similar to the program in the previous example, we need a LanguageServiceHost. The LanguageServiceHost augments the concept of a file with a version, isOpen flag, and a ScriptSnapshot. Version allows the language service to track changes to files. isOpen tells the language service to keep AST in memory as the file is in use. ScriptSnapshot is an abstraction over text that allows the language service to query for changes.

```TypeScript
/// <reference path="typings/node/node.d.ts" />

import * as fs from "fs";
import * as ts from "typescript";

function watch(rootFileNames: string[], options: ts.CompilerOptions) {
    const files: ts.Map<{ version: number }> = {};

    // initialize the list of files
    rootFileNames.forEach(fileName => {
        files[fileName] = { version: 0 };
    });

    // Create the language service host to allow the LS to communicate with the host
    const servicesHost: ts.LanguageServiceHost = {
        getScriptFileNames: () => rootFileNames,
        getScriptVersion: (fileName) => files[fileName] && files[fileName].version.toString(),
        getScriptSnapshot: (fileName) => {
            if (!fs.existsSync(fileName)) {
                return undefined;
            }

            return ts.ScriptSnapshot.fromString(fs.readFileSync(fileName).toString());
        },
        getCurrentDirectory: () => process.cwd(),
        getCompilationSettings: () => options,
        getDefaultLibFileName: (options) => ts.getDefaultLibFilePath(options),
    };

    // Create the language service files
    const services = ts.createLanguageService(servicesHost, ts.createDocumentRegistry())

    // Now let's watch the files
    rootFileNames.forEach(fileName => {
        // First time around, emit all files
        emitFile(fileName);

        // Add a watch on the file to handle next change
        fs.watchFile(fileName,
            { persistent: true, interval: 250 },
            (curr, prev) => {
                // Check timestamp
                if (+curr.mtime <= +prev.mtime) {
                    return;
                }

                // Update the version to signal a change in the file
                files[fileName].version++;

                // write the changes to disk
                emitFile(fileName);
            });
    });

    function emitFile(fileName: string) {
        let output = services.getEmitOutput(fileName);

        if (!output.emitSkipped) {
            console.log(`Emitting ${fileName}`);
        }
        else {
            console.log(`Emitting ${fileName} failed`);
            logErrors(fileName);
        }

        output.outputFiles.forEach(o => {
            fs.writeFileSync(o.name, o.text, "utf8");
        });
    }

    function logErrors(fileName: string) {
        let allDiagnostics = services.getCompilerOptionsDiagnostics()
            .concat(services.getSyntacticDiagnostics(fileName))
            .concat(services.getSemanticDiagnostics(fileName));

        allDiagnostics.forEach(diagnostic => {
            let message = ts.flattenDiagnosticMessageText(diagnostic.messageText, "\n");
            if (diagnostic.file) {
                let { line, character } = diagnostic.file.getLineAndCharacterOfPosition(diagnostic.start);
                console.log(`  Error ${diagnostic.file.fileName} (${line + 1},${character + 1}): ${message}`);
            }
            else {
                console.log(`  Error: ${message}`);
            }
        });
    }
}

// Initialize files constituting the program as all .ts files in the current directory
const currentDirectoryFiles = fs.readdirSync(process.cwd()).
    filter(fileName=> fileName.length >= 3 && fileName.substr(fileName.length - 3, 3) === ".ts");

// Start the watcher
watch(currentDirectoryFiles, { module: ts.ModuleKind.CommonJS });
```

## Transpiling a single file

Currently TypeScript exposes two functions for this purpose: `transpileModule` and `transpile` (**which is deprecated**).
Note that regardless of the name, **each one assumes that the input file is a module**.

Here is the relevant signature of `transpileModule`:

```ts
export interface TranspileOptions {
    compilerOptions?: CompilerOptions;
    fileName?: string;
    reportDiagnostics?: boolean;
    moduleName?: string;
    renamedDependencies?: Map<string>;
}

export interface TranspileOutput {
    outputText: string;
    diagnostics?: Diagnostic[];
    sourceMapText?: string;
}

/*
 * This function will compile source text from 'input' argument using specified compiler options.
 * If not options are provided - it will use a set of default compiler options.
 * Extra compiler options that will unconditionally be used by this function are:
 * - isolatedModules = true
 * - allowNonTsExtensions = true
 * - noLib = true
 * - noResolve = true
 */    
export function transpileModule(input: string, transpileOptions: TranspileOptions): TranspileOutput 
```

and here is the appropriate version of `transpile`:

```ts
export function transpile(input: string, compilerOptions?: CompilerOptions, fileName?: string, diagnostics?: Diagnostic[], moduleName?: string): string;
```

> Historical note: initially only `transpile` function existed, however it was pretty difficult to extend (i.e to add new input parameters or return some extra information like source maps) without breaking existing consumers. As a result `transpile` is currently considered deprecated and superseded by `transpileModule`.

```ts
var ts = require("typescript");
var content = 
    "import {f} from \"foo\"\n" +
    "export var x = f()";

var compilerOptions = { module: ts.ModuleKind.System };

var res1 = ts.transpileModule(content, {compilerOptions: compilerOptions, moduleName: "myModule2"});
console.log(res1.outputText);

console.log("============")

var res2 = ts.transpile(content, compilerOptions, /*fileName*/ undefined, /*diagnostics*/ undefined, /*moduleName*/ "myModule1");
console.log(res2);
```

Usually TypeScript compiler uses file extension to determine if file should be parsed as '.tsx' or '.ts'. The same rule is applied during single file transpilation if the file name is provided.
If the file name is omitted, then compiler will check if the `jsx` options is specified.
If it is set and is not `JsxEmit.None`, then source text will be interpreted as '.tsx'.

## Customizing module resolution

You can override the standard way the compiler uses to resolve modules by implementing optional method: `CompilerHost.resolveModuleNames`:
> `CompilerHost.resolveModuleNames(moduleNames: string[], containingFile: string): string[]`. 

The method is given a list of module names in a file, and is expected to return an array of size `moduleNames.length`, each element of the array stores either:

*  an instance of `ResolvedModule` with non-empty property `resolvedFileName` - resolution for corresponding name from `moduleNames` array or 
* `undefined` if module name cannot be resolved. 

You can invoke the standard module resolution process via calling `resolveModuleName`:
> `resolveModuleName(moduleName: string, containingFile: string, options: CompilerOptions, moduleResolutionHost: ModuleResolutionHost): ResolvedModuleNameWithFallbackLocations`. 

This function returns an object that stores result of module resolution (value of `resolvedModule` property) as well as list of file names that were considered candidates before making current decision. 

```ts
/// <reference path="typings/node/node.d.ts" />

import * as ts from "typescript";
import * as path from "path";

function createCompilerHost(options: ts.CompilerOptions, moduleSearchLocations: string[]): ts.CompilerHost {
    return {
        getSourceFile,
        getDefaultLibFileName: () => "lib.d.ts",
        writeFile: (fileName, content) => ts.sys.writeFile(fileName, content),
        getCurrentDirectory: () => ts.sys.getCurrentDirectory(),
        getCanonicalFileName: fileName => ts.sys.useCaseSensitiveFileNames ? fileName : fileName.toLowerCase(),
        getNewLine: () => ts.sys.newLine,
        useCaseSensitiveFileNames: () => ts.sys.useCaseSensitiveFileNames,
        fileExists,
        readFile,
        resolveModuleNames
    }
    
    function fileExists(fileName: string): boolean {
        return ts.sys.fileExists(fileName);
    }
    
    function readFile(fileName: string): string {
        return ts.sys.readFile(fileName);
    }
    
    function getSourceFile(fileName: string, languageVersion: ts.ScriptTarget, onError?: (message: string) => void) {
        const sourceText = ts.sys.readFile(fileName);
        return sourceText !== undefined ? ts.createSourceFile(fileName, sourceText, languageVersion) : undefined;
    }

    function resolveModuleNames(moduleNames: string[], containingFile: string): ts.ResolvedModule[] {
        return moduleNames.map(moduleName => {
            // try to use standard resolution
            let result = ts.resolveModuleName(moduleName, containingFile, options, {fileExists, readFile});
            if (result.resolvedModule) {
                return result.resolvedModule;
            }

            // check fallback locations, for simplicity assume that module at location should be represented by '.d.ts' file
            for (const location of moduleSearchLocations) {
                const modulePath = path.join(location, moduleName + ".d.ts");
                if (fileExists(modulePath)) {
                    return { resolvedFileName: modulePath }
                }
            } 

            return undefined;
        });
    }
}

function compile(sourceFiles: string[], moduleSearchLocations: string[]): void {
    const options: ts.CompilerOptions = { module: ts.ModuleKind.AMD, target: ts.ScriptTarget.ES5 };
    const host = createCompilerHost(options, moduleSearchLocations);
    const program = ts.createProgram(sourceFiles, options, host);
    
    /// do something with program...
}
```

### Using the Type Checker

In this example we will walk the AST and use the checker to serialize class information.
We'll use the type checker to get symbol and type information, while grabbing JSDoc comments for exported classes, their constructors, and respective constructor parameters.

```ts
/// <reference path="typings/node/node.d.ts" />

import * as ts from "typescript";
import * as fs from "fs";

interface DocEntry {
    name?: string,
    fileName?: string,
    documentation?: string,
    type?: string,
    constructors?: DocEntry[],
    parameters?: DocEntry[],
    returnType?: string
};

/** Generate documentation for all classes in a set of .ts files */
function generateDocumentation(fileNames: string[], options: ts.CompilerOptions): void {
    // Build a program using the set of root file names in fileNames
    let program = ts.createProgram(fileNames, options);
    
    // Get the checker, we will use it to find more about classes
    let checker = program.getTypeChecker();

    let output: DocEntry[] = [];

    // Visit every sourceFile in the program    
    for (const sourceFile of program.getSourceFiles()) {
        // Walk the tree to search for classes
        ts.forEachChild(sourceFile, visit);
    }

    // print out the doc
    fs.writeFileSync("classes.json", JSON.stringify(output, undefined, 4));

    return;

    /** visit nodes finding exported classes */    
    function visit(node: ts.Node) {
        // Only consider exported nodes
        if (!isNodeExported(node)) {
            return;
        }

        if (node.kind === ts.SyntaxKind.ClassDeclaration) {
            // This is a top level class, get its symbol
            let symbol = checker.getSymbolAtLocation((<ts.ClassDeclaration>node).name);
            output.push(serializeClass(symbol));
            // No need to walk any further, class expressions/inner declarations
            // cannot be exported
        }
        else if (node.kind === ts.SyntaxKind.ModuleDeclaration) {
            // This is a namespace, visit its children
            ts.forEachChild(node, visit);
        }
    }

    /** Serialize a symbol into a json object */    
    function serializeSymbol(symbol: ts.Symbol): DocEntry {
        return {
            name: symbol.getName(),
            documentation: ts.displayPartsToString(symbol.getDocumentationComment()),
            type: checker.typeToString(checker.getTypeOfSymbolAtLocation(symbol, symbol.valueDeclaration))
        };
    }

    /** Serialize a class symbol infomration */
    function serializeClass(symbol: ts.Symbol) {
        let details = serializeSymbol(symbol);

        // Get the construct signatures
        let constructorType = checker.getTypeOfSymbolAtLocation(symbol, symbol.valueDeclaration);
        details.constructors = constructorType.getConstructSignatures().map(serializeSignature);
        return details;
    }

    /** Serialize a signature (call or construct) */
    function serializeSignature(signature: ts.Signature) {
        return {
            parameters: signature.parameters.map(serializeSymbol),
            returnType: checker.typeToString(signature.getReturnType()),
            documentation: ts.displayPartsToString(signature.getDocumentationComment())
        };
    }

    /** True if this is visible outside this file, false otherwise */
    function isNodeExported(node: ts.Node): boolean {
        return (node.flags & ts.NodeFlags.Export) !== 0 || (node.parent && node.parent.kind === ts.SyntaxKind.SourceFile);
    }
}

generateDocumentation(process.argv.slice(2), {
    target: ts.ScriptTarget.ES5, module: ts.ModuleKind.CommonJS
});
```

to try this:

```shell
tsc docGenerator.ts --m commonjs 
node docGenerator.js test.ts
```

Passing an input like:

```ts
/** 
 * Documentation for C 
 */
class C { 
    /** 
     * constructor documentation
     * @param a my parameter documentation
     * @param b another parameter documentation
     */
    constructor(a: string, b: C) { }
}
```

We should get output like:

```json
[
    {
        "name": "C",
        "documentation": "Documentation for C ",
        "type": "typeof C",
        "constructors": [
            {
                "parameters": [
                    {
                        "name": "a",
                        "documentation": "my parameter documentation",
                        "type": "string"
                    },
                    {
                        "name": "b",
                        "documentation": "another parameter documentation",
                        "type": "C"
                    }
                ],
                "returnType": "C",
                "documentation": "constructor documentation"
            }
        ]
    }
]
```
