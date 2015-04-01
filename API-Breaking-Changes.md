# TypeScript 1.5

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


### APIs made internal as they are not intended for use outside of the compiler
We did some cleanup to the public interfaces, here is the full list of changes:
- Commit [2ee134c6b3c0ec](https://github.com/Microsoft/TypeScript/commit/2ee134c6b3c0ece87591e8abc9db833ebb7675cc)  
- Commit [35dde28d44122c](https://github.com/Microsoft/TypeScript/commit/35dde28d44122c90aaae7695f56bf22c1d848486)  
- Commit [c9ef4db99ac93bb1c166a](https://github.com/Microsoft/TypeScript/commit/c9ef4db99ac93bb1c166aa9af495453eeceea279)  
* Commit [b4e5d5b0b460cc88a10db](https://github.com/Microsoft/TypeScript/commit/b4e5d5b0b460cc88a10dbfdb0a935fb33b534ab2)  

