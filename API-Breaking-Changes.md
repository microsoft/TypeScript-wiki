# TypeScript 1.5

### DOM interface changes
- Property ``selection`` is removed from type ``Document``
- Property ``clipboardData`` is removed from type ``Window``
- Removed interface ``MSEventAttachmentTarget``
- Properties ``onresize``, ``disabled``, ``uniqueID``, ``removeNode``, ``fireEvent``, ``currentStyle``, ``runtimeStyle`` are removed from type ``HTMLElement``
- Property ``url`` is removed from type ``Event``
- Properties ``execScript``, ``navigate``, ``item`` are removed from type ``Window``
- Properties ``documentMode``, ``parentWindow``, ``createEventObject`` are removed from type ``Document``
- Property ``parentWindow`` is removed from type ``HTMLDocument``
- Property ``setCapture`` does not exist anywhere now
- Property ``releaseCapture`` does not exist anywhere now
- Properties ``setAttribute``, ``styleFloat``, ``pixelLeft`` are removed from type ``CSSStyleDeclaration``
- Property ``selectorText`` is removed from type ``CSSRule``
- ``CSSStyleSheet.rules`` is of type ``CSSRuleList`` instead of ``MSCSSRuleList``
- ``documentElement`` is of type ``Element`` instead of ``HTMLElement``
- ``Event`` has a new required property ``returnValue``
- ``Node`` has a new required property ``baseURI``
- ``Element`` has a new required property ``classList``
- Properties ``MSPOINTER_TYPE_MOUSE``, ``MSPOINTER_TYPE_TOUCH`` are removed from type ``MSPointerEvent``
- ``CSSStyleRule`` has a new required property ``readonly``
- Property ``execUnsafeLocalFunction`` is removed from type ``MSApp``
- Global method ``toStaticHTML`` is removed
- ``HTMLCanvasElement.getContext`` now returns ``CanvasRenderingContext2D | WebGLRenderingContex``
- Removed extension types ``Dataview``, ``Weakmap``, ``Map``, ``Set``
- ``XMLHttpRequest.send`` has two overloads ``send(data?: Document): void;`` and ``send(data?: String): void;``
- ``window.orientation`` is of type ``string`` instead of ``number``

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
- `SourceFile .filename` => `SourceFile.fileName`
- `FileReference`.filename => `FileReference.fileName`
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