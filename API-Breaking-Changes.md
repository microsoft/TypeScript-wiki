# TypeScript 3.0

- The deprecated internal method `LanguageService#getSourceFile` has been removed. See #24540.
- The deprecated function `TypeChecker#getSymbolDisplayBuilder` and associated interfaces have been removed. See [#25331](https://github.com/Microsoft/TypeScript/pull/25331). The emitter and node builder should be used instead.

# TypeScript 2.8
- `getJsxIntrinsicTagNames` has been removed and replaced with `getJsxIntrinsicTagNamesAt`, which requires a node to use as the location to look up the valid intrinsic names at (to handle locally-scoped JSX namespaces).

# TypeScript 2.6

- Some services methods (`getCompletionEntryDetails` and `getCompletionEntrySymbols`) have additional parameters. Plugins that wrap the language service must pass these parameters along to the original implementation. See [#19507](https://github.com/Microsoft/TypeScript/pull/19507#issuecomment-340600363)

# TypeScript 2.5
- `Symbol.name`, `Symbol.getName()`, and `Identifier.text` are all now of type `__String`. This is a special branded string to help track where strings are appropriately escaped and prevent their misuse. `escapeIdentifier` and `unescapeIdentifier` has been renamed to `escapeLeadingUnderscores` and `unescapeLeadingUnderscores` and had their types updated accordingly. Deprecated versions of `escapeIdentifier` and `unescapeIdentifier` still exist with the old name and type signature, however they will be removed in a future version. See [#16915](https://github.com/Microsoft/TypeScript/issues/16915).

# TypeScript 2.4

- The following types/namespaces are now string enums: `ts.Extension`, `ts.ScriptElementKind`, `ts.HighlightSpanKind`, `ts.ClassificationTypeNames`, `protocol.CommandTypes`, `protocol.IndentStyle`, `protocol.JsxEmit`, `protocol.ModuleKind`, `protocol.ModuleResolutionKind`, `protocol.NewLineKind`, and `protocol.ScriptTarget`. Also, `ts.CommandNames` is now an alias for `protocol.CommandTypes`. See [#15966](https://github.com/Microsoft/TypeScript/pull/15966) and [#16425](https://github.com/Microsoft/TypeScript/pull/16425).

- The type `EnumLiteralType` was removed and `LiteralType` is used instead. `LiteralType` also replaces `.text` with a `.value` which may be either a number or string. See [String valued members in enums](https://github.com/Microsoft/TypeScript/pull/15486).

- `Declaration` does not have a `name` property. TypeScript now recognize assignments in .js files as declarations in certain contexts, e.g. `func.prototype.method = function() {..}` will be a declaration of member `method` on `func`. As a result `Declaration` is not guaranteed to have a `name` property as before. A new type was introduced `NamedDeclaration` to take the place of `Declaration`, and `Declaration` moved to be the base type of both `NamedDeclaration` and `BinaryExpression`.
Casting to `NamedDeclaration` should be safe for non .js declarations.
See https://github.com/Microsoft/TypeScript/pull/15594 for more details.

# TypeScript 2.2

- `ts.Map<T>` is now a native `Map<string, T>` or a shim. This affects the `SymbolTable` type, exposed by `Symbol.members`, `Symbol.exports`, and `Symbol.globalExports`.

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