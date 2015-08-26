## Overview
The presence of a `tsconfig.json` file in a directory indicates that the directory is the root of a TypeScript project. The `tsconfig.json` file specifies the root files and the compiler options required to compile the project. `tsconfig.json` is supported starting with TypeScript 1.5. A project is compiled in one of the following ways:

## Using tsconfig.json
* By invoking tsc with no input files, in which case the compiler searches for the `tsconfig.json` file starting in the current directory and continuing up the parent directory chain.
* By invoking tsc with no input files and a `-project` (or just `-p`) command line option that specifies the path of a directory containing a `tsconfig.json` file.

When input files are specified on the command line, `tsconfig.json` files are ignored.

## Examples
Example `tsconfig.json` files:

* Using the `"files"` property
```json
{
    "compilerOptions": {
        "module": "commonjs",
        "noImplicitAny": true,
        "removeComments": true,
        "preserveConstEnums": true,
        "out": "../../built/local/tsc.js",
        "sourceMap": true
    },
    "files": [
        "core.ts",
        "sys.ts",
        "types.ts",
        "scanner.ts",
        "parser.ts",
        "utilities.ts",
        "binder.ts",
        "checker.ts",
        "emitter.ts",
        "program.ts",
        "commandLineParser.ts",
        "tsc.ts",
        "diagnosticInformationMap.generated.ts"
    ]
}
```

* Using the `"exclude"` property
```json
{
    "compilerOptions": {
        "module": "commonjs",
        "noImplicitAny": true,
        "removeComments": true,
        "preserveConstEnums": true,
        "out": "../../built/local/tsc.js",
        "sourceMap": true
    },
    "exclude": [
        "node_modules",
        "wwwroot"
    ]
}
```

## Details 
The `"compilerOptions"` property can be omitted, in which case the compiler's defaults are used. For more details about supported compiler options see [[Compiler Options]] documentation.

If no `"files"` property is present in a `tsconfig.json`, the compiler defaults to including all files in the containing directory and subdirectories. When a `"files"` property is specified, only those files are included.

If the `"exclude"` property is specified, the compiler includes all files in the containing directory and subdirectories except for those files that are excluded.

The `"files"` property cannot be used in conjunction with the `"exclude"` property. If both are specified then the `"files"` property takes precedence.

Files referenced by files that are included are also included. Similarly, if a file is referenced by another file it cannot be excluded unless the referencing file is also excluded.

A `tsconfig.json` file is permitted to be completely empty, which compiles all files in the containing directory and subdirectories with the default compiler options.

Compiler options specified on the command line override those specified in the `tsconfig.json` file.

## Schema
Schema can be found at: http://json.schemastore.org/tsconfig
