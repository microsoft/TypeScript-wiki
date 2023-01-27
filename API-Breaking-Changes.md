# TypeScript 5.0

- TypeScript is now itself implemented using modules (though, the package still contains bundled outputs).
  - The exported API is no longer defined as a "configurable" object, so operations which attempt to modify the package at runtime such as `const ts = require("ts"); ts.readJson = ...` will throw.
  - The output files have changed significantly; if you are patching TypeScript, you will definitely need to change your patches.
- `typescriptServices.js` has been removed; this file was identical to `typescript.js`, the entrypoint for our npm package.
- `protocol.d.ts` is no longer included in the package; use `tsserverlibrary.d.ts`'s `ts.server.protocol` namespace instead.
  - Some elements of the protocol are not actually exported by the `ts.server.protocol` namespace, but were emitted in the old `protocol.d.ts` file, and may need to be accessed off of the `ts` namespace instead. See https://github.com/microsoft/vscode/pull/163365 for an potential way to minimize changes to protocol-using codebases.
- The TypeScript package now targets ES2018, requiring Node 10 or newer. Prior to 5.0, our package targeted ES5 syntax and the ES2015 library.
  - Before 5.0 is released, we may increase this target to Node 12 (for ESM support).
- `ts.Map`, `ts.Set`, `ts.ESMap`, `ts.Iterator`, and associated types have been removed. The native `Map`, `Set`, `Iterator` and associated types should be used instead.
- The `ts.Collection` and `ts.ReadonlyCollection` types have been removed. These types were unused in our public API, and were declared with the old `Map`/`Set` types (also removed in 5.0).
- The `ts.Push` type has been removed. This type was only used twice in our API, and its uses have been replaced with arrays for consistency with other parts of our API.
- `BuilderProgramHost` no longer requires method `useCaseSensitiveFileNames` since its used from `program`.
- The TypeScript compiler is now compiled with `strictFunctionTypes`; to allow this, certain public AST visitor APIs have been modified to better reflect their underlying guarantees, as well as various corrections. The resulting API should be one that is more compatible with projects which also enable `strictFunctionTypes` (a recommended option enabled by `strict`).
  - The `VisitResult` type is no longer `undefined` by default; if you have written `VisitResult<Node>`, you may need to rewrite it as `VisitResult<Node | undefined>` to reflect that your visitor may return undefined.
  - Visitor-using APIs now correctly reflect the type of the output, including whether it passed a given type guard test, and whether or not it may be undefined. In order to get the type you expect, you may need to pass a `test` parameter to verify your expectations and then check the result for `undefined` (or, modify your visitor to return a more specific type).
- `typingOptions` along with its property `enableAutoDiscovery` which was deprecated for a long time is not supported any more in `tsconfig.json` and `jsconfig.json`. Use `typeAcquisition` in the config instead.

# TypeScript 4.9

## `substitute` Replaced With `constraint` on `SubstitutionType`s

As part of an optimization on substitution types, `SubstitutionType` objects no longer contain the `substitute` property representing the effective substitution (usually an intersection of the base type and the implicit constraint) - instead, they just contain the `constraint` property.

For more details, [read more on the original pull request](https://github.com/microsoft/TypeScript/pull/50397).

# TypeScript 4.8

## Decorators are placed on `modifiers` on TypeScript's Syntax Trees

The current direction of decorators in TC39 means that TypeScript will have to handle a break in terms of placement of decorators.
Previously, TypeScript assumed decorators would always be placed prior to all keywords/modifiers.
For example

```ts
@decorator
export class Foo {
  // ...
}
```

Decorators as currently proposed do not support this syntax.
Instead, the `export` keyword must precede the decorator.

```ts
export @decorator class Foo {
  // ...
}
```

Unfortunately, TypeScript's trees are *concrete* rather than *abstract*, and our architecture expects syntax tree node fields to be entirely ordered before or after each other.
To support both legacy decorators and decorators as proposed, TypeScript will have to gracefully parse, and intersperse, modifiers and decorators.

To do this, it exposes a new type alias called `ModifierLike` which is a `Modifier` or a `Decorator`.

```ts
export type ModifierLike = Modifier | Decorator;
```

Decorators are now placed in the same field as `modifiers` which is now a `NodeArray<ModifierLike>` when set, and the entire field is deprecated.

```diff
- readonly modifiers?: NodeArray<Modifier> | undefined;
+ /**
+  * @deprecated ...
+  * Use `ts.canHaveModifiers()` to test whether a `Node` can have modifiers.
+  * Use `ts.getModifiers()` to get the modifiers of a `Node`.
+  * ...
+  */
+ readonly modifiers?: NodeArray<ModifierLike> | undefined;
```

All existing `decorators` properties have been marked as deprecated and will always be `undefined` if read.
The type has also been changed to `undefined` so that existing tools know to handle them correctly.

```diff
- readonly decorators?: NodeArray<Decorator> | undefined;
+ /**
+  * @deprecated ...
+  * Use `ts.canHaveDecorators()` to test whether a `Node` can have decorators.
+  * Use `ts.getDecorators()` to get the decorators of a `Node`.
+  * ...
+  */
+ readonly decorators?: undefined;
```

To avoid all deprecation warnings and other issues, TypeScript now exposes four new functions.
There are individual predicates for testing whether a node has support modifiers and decorators, along with respective accessor functions for grabbing them.

```ts
function canHaveModifiers(node: Node): node is HasModifiers;
function getModifiers(node: HasModifiers): readonly Modifier[] | undefined;

function canHaveDecorators(node: Node): node is HasDecorators;
function getDecorators(node: HasDecorators): readonly Decorator[] | undefined;
```

As an example of how to access modifiers off of a node, you can write

```ts
const modifiers = canHaveModifiers(myNode) ? getModifiers(myNode) : undefined;
```

With the note that each call to `getModifiers` and `getDecorators` may allocate a new array.

For more information, see changes around

* [the restructuring of our tree nodes](https://github.com/microsoft/TypeScript/pull/49089)
* [the deprecations](https://github.com/microsoft/TypeScript/pull/50343)
* [exposing the predicate functions](https://github.com/microsoft/TypeScript/pull/50399)

# TypeScript 4.7

- `resolveTypeReferenceDirectives` (both the services and global ts version) now accept an array of `FileReference`s as a first argument. If you reimplement `resolveTypeReferenceDirectives`, you need to handle both the `string[]` and `FileReference[]` cases now.

# TypeScript 4.5

- `factory.createImportSpecifier` and `factory.updateImportSpecifier` now take an `isTypeOnly` parameter:

  ```diff
  - createImportSpecifier(propertyName: Identifier | undefined, name: Identifier): ImportSpecifier;
  + createImportSpecifier(isTypeOnly: boolean, propertyName: Identifier | undefined, name: Identifier): ImportSpecifier;
  - updateImportSpecifier(node: ImportSpecifier, propertyName: Identifier | undefined, name: Identifier): ImportSpecifier;
  + updateImportSpecifier(node: ImportSpecifier, isTypeOnly: boolean, propertyName: Identifier | undefined, name: Identifier): ImportSpecifier;
  ```

  You can read more about this change at the [implementing PR](https://github.com/microsoft/TypeScript/pull/45998).

# TypeScript 4.2

- `visitNode`'s `lift` Takes a `readonly Node[]` Instead of a `NodeArray<Node>`

   The `lift` function in the `visitNode` API now takes a `readonly Node[]`.
   You can [see details of the change here](https://github.com/microsoft/TypeScript/pull/42000).


# TypeScript 4.1

- Type Arguments in JavaScript Are Not Parsed as Type Arguments

  Type arguments were already not allowed in JavaScript, but in TypeScript 4.1, the parser will parse them in a more spec-compliant way.
  So when writing the following code in a JavaScript file:

  ```ts
  f<T>(100)
  ```

  TypeScript will parse it as the following JavaScript:

  ```js
  (f < T) > (100)
  ```

  This may impact you if you were leveraging TypeScript's API to parse type constructs in JavaScript files, which may have occurred when trying to parse Flow files.

  [See more details here](https://github.com/microsoft/TypeScript/pull/36673).

# TypeScript 4.0

- TypeScript provides a set of "factory" functions for producing syntax tree nodes; however, TypeScript 4.0 provides a new node factory API. For TypeScript 4.0 we've made the decision to deprecate these older functions in favor of the new ones. For more details, [read up on the relevant pull request for this change](https://github.com/microsoft/TypeScript/pull/35282).

- `TupleTypeNode.elementTypes` renamed to `TupleTypeNode.elements`.
- `KeywordTypeNode` is no longer used to represent `this` and `null` types. `null` now gets a `LiteralTypeNode`, `this` now always gets a `ThisTypeNode`.
- `TypeChecker.typeToTypeNode` now correctly produces a `LiteralTypeNode` for `true` and `false` types, which matches the behavior in the parser. Prior to this the checker was incorrectly returning the `true` and `false` tokens themselves, which are indistinguishable from expressions when traversing a tree.

# TypeScript 3.8

- The mutable property `disableIncrementalParsing` has been removed. It was untested and, at least on GitHub, unused by anyone. Incremental parsing can no longer be disabled.

# TypeScript 3.7

- the `typeArguments` property has been removed from the `TypeReference` interface, and the `getTypeArguments` method on `TypeChecker` instances should be used instead. This change was necessary to defer resolution of type arguments in order to support [recursive type references](https://github.com/microsoft/TypeScript/pull/33050).

  As a workaround, you can define a helper function to support multiple versions of TypeScript.

  ```ts
  function getTypeArguments(checker: ts.TypeChecker, typeRef: ts.TypeReference) {
      return checker.getTypeArguments?.(typeRef) ?? (typeRef as any).typeArguments;
  }
  ```


# TypeScript 3.1

- `SymbolFlags.JSContainer` has been renamed to `SymbolFlags.Assignment` to reflect that Typescript now supports expando assignments to functions.

# TypeScript 3.0

- The deprecated internal method `LanguageService#getSourceFile` has been removed. See [#24540](https://github.com/microsoft/TypeScript/pull/24540).
- The deprecated function `TypeChecker#getSymbolDisplayBuilder` and associated interfaces have been removed. See [#25331](https://github.com/Microsoft/TypeScript/pull/25331). The emitter and node builder should be used instead.
- The deprecated functions `escapeIdentifier` and `unescapeIdentifier` have been removed. Due to changing how the identifier name API worked in general, they have been identity functions for a few releases, so if you need your code to behave the same way, simply removing the calls should be sufficient. Alternatively, the typesafe `escapeLeadingUnderscores` and `unescapeLeadingUnderscores` should be used if the types indicate they are required (as they are used to convert to or from branded `__String` and `string` types).
- The `TypeChecker#getSuggestionForNonexistentProperty`, `TypeChecker#getSuggestionForNonexistentSymbol`, and `TypeChecker#getSuggestionForNonexistentModule` methods have been made internal, and are no longer part of our public API. See [#25520](https://github.com/Microsoft/TypeScript/pull/25520).

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
See [#15594](https://github.com/Microsoft/TypeScript/pull/15594) for more details.

# TypeScript 2.2

- `ts.Map<T>` is now a native `Map<string, T>` or a shim. This affects the `SymbolTable` type, exposed by `Symbol.members`, `Symbol.exports`, and `Symbol.globalExports`.

# TypeScript 2.1

- `ParseConfigHost` now requires a new member `readFile` to support [configuration inheritance](https://github.com/Microsoft/TypeScript/pull/9941).

# TypeScript 1.9

- [`LanguageService.getSourceFile` has been removed](https://github.com/Microsoft/TypeScript/pull/7584); `LanguageService.getProgram().getSourceFile` should be used instead.

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

The two files exposed helpers in the past that were not part of the supported TypeScript API. If you were using any of these APIs please file an issue to re-expose them; requests for exposing helper APIs will be triaged on a case-by-case basis.

For more information please see the [full change](https://github.com/Microsoft/TypeScript/pull/2692).
