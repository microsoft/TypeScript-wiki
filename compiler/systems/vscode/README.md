### VS Code + TypeScript

VS Code has three extensions related to TypeScript:

- `extensions/typescript` - this is an `npm install`'d version of TypeScript.
- `extensions/typescript-basics` - this is a extension which provides
  [language grammars and defines the language of TypeScript](https://github.com/microsoft/vscode/commit/e23c58b3aba76f25bb99400619d39f285eeec9e1#diff-cdbcc33fea0f5bd15137cf1750d69776)
  inside VS Code
- `extensions/typescript-language-features` - this extends the TypeScript language support with commands,
  auto-complete et al.

### [`typescript-language-features`](https://github.com/microsoft/vscode/tree/master/extensions/typescript-language-features)

A large amount of the work happens in the [`TypeScriptServiceClient`][1] which is the VS Code side of the
TSServer.

[1]:
  https://github.com/microsoft/vscode/blob/master/extensions/typescript-language-features/src/typescriptServiceClient.ts#L75
