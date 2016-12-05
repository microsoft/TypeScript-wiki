# TypeScript 2.1

- `ParseConfigHost` now require a new member `readFile` to support [configuration inheritance](https://github.com/Microsoft/TypeScript/pull/9941).

# TypeScript 1.9

- `LanguageService.getSourceFile` is removed (https://github.com/Microsoft/TypeScript/pull/7584), `LanguageService.getProgram().getSourceFile` should be used instead.

# TypeScript 1.7

- `ts.parseConfigFile` has been renamed to `ts.parseJsonConfigFileContent`

# TypeScript 1.6

### CompilerHost interface change (comparing to TypeScript 1.6 beta)
- return type of `CompilerHost.resolveModuleNames` was changed from `string[]` to `ResolvedModule[]`. Extra optional property `isExternalLibraryImport` in [ResolvedModule](https://github.com/Microsoft/TypeScript/blob/026632bca8b13a7f180ae8226e109d296480ddad/src/compiler/types.ts#L2274) interface denotes if `Program` should apply some particular set of policies to the resolved file. For example if Node resolver has resolved non-relative module name to the file in 'node_modules', then this file:
  - should be a 'd.ts' file
  - should be an external module
  - should not contain tripleslash references.

  Rationale: files containing external typings should not pollute global scope (to avoid conflicts between different versions of the same package). Also such files should never be added to the list of compiled files (otherwise compiled .ts file might overwrite actual .js file with implementation of the package)

# TypeScript 1.5

### Program interface changes
- `TypeChecker.emitFiles` is no longer available; use `Program.emit` instead.
- Getting diagnostics are now all centralized on Program,
   - for Syntactic diagnostics for a single file use: `Program.getSyntacticDiagnostics(sourceFile)`
   - for Syntactic diagnostics for all files use: `Program.getSyntacticDiagnostics()`
   - for Semantic diagnostics for a single file use: `Program.getSemanticDiagnostics(sourceFile)`
   - for Semantic diagnostics for all files use: `Program.getSemanticDiagnostics()`
   - for compiler options and global diagnostics use: `Program.getGlobalDiagnostics()`
> Tip: use ts.getPreEmitDiagnostics(program) to get syntactic, semantic, and global diagnostics for all files

### All usages of 'filename' and 'Filename' changed to 'fileName' and 'FileName'
Here are the details:
- `CompilerHost.getDefaultLibFilename` => `CompilerHost.getDefaultLibFileName`
- `SourceFile.filename` => `SourceFile.fileName`
- `FileReference.filename` => `FileReference.fileName`
- `LanguageServiceHost.getDefaultLibFilename` => `LanguageServiceHost.getDefaultLibFileName`
- `LanguageServiceShimHost.getDefaultLibFilename` => `LanguageServiceShimHost.getDefaultLibFileName`


The full list of APIs can be found in [this commit](https://github.com/Microsoft/TypeScript/commit/de13648c9f87e0da272f5ed14767afb2c8788322)

### The `syntacticClassifierAbsent` parameter for the Classifier.getClassificationsForLine is now required
See [Pull Request #2051](https://github.com/Microsoft/TypeScript/pull/2051) for more details.

### Changes to TextChange
`TextChange.start` and `TextChange.length` became properties instead of methods.

### SourceFile.getLineAndCharacterFromPosition
`SourceFile.getLineAndCharacterFromPosition` became `SourceFile.getLineAndCharacterOfPosition`

### APIs made internal as they are not intended for use outside of the compiler
We did some cleanup to the public interfaces, here is the full list of changes:
- Commit [2ee134c6b3c0ec](https://github.com/Microsoft/TypeScript/commit/2ee134c6b3c0ece87591e8abc9db833ebb7675cc)  
- Commit [35dde28d44122c](https://github.com/Microsoft/TypeScript/commit/35dde28d44122c90aaae7695f56bf22c1d848486)  
- Commit [c9ef4db99ac93bb1c166a](https://github.com/Microsoft/TypeScript/commit/c9ef4db99ac93bb1c166aa9af495453eeceea279)  
* Commit [b4e5d5b0b460cc88a10db](https://github.com/Microsoft/TypeScript/commit/b4e5d5b0b460cc88a10dbfdb0a935fb33b534ab2)  


### `typescript_internal.d.ts` and `typescriptServices_internal.d.ts` have been removed

The two files exposed helpers in the past that were not part of the supported TypeScript API. If you were using any of these APIs please file an issue to re-expose them; requests for exposing helper APIs will be triaged on a case-per-case basis.

For more information please see the [full change](https://github.com/Microsoft/TypeScript/pull/2692).