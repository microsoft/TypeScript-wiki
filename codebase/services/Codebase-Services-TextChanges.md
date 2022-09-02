# Text Changes

The majority of this file is devoted to a class called the [`ChangeTracker`][0]. This class is nearly always
created via `ChangeTracker.with` where you would give it a context object.

Here is an example context object:

```ts
{
  cancellationToken:CancellationTokenObject {cancellationToken: TestCancellationToken}
  errorCode:2304
  formatContext:Object {options: Object, getRule: }
  host:NativeLanguageServiceHost {cancellationToken: TestCancellationToken, settings: Object, sys: System, …}
  preferences:Object {}
  program:Object {getRootFileNames: , getSourceFile: , getSourceFileByPath: , …}
  sourceFile:SourceFileObject {pos: 0, end: 7, flags: 65536, …}
  span:Object {start: 0, length: 6}
}
```

You only really see `ChangeTrack` in use within the codefixes and refactors given that the other case where
TypeScript emits files is a single operation of emission.

The change tracker keeps track of individual changes to be applied to a file. There are [currently][1] four main
APIs that it works with:
`type Change = ReplaceWithSingleNode | ReplaceWithMultipleNodes | RemoveNode | ChangeText;`

The `ChangeTrack` class is then used to provide high level API to describe the sort of changes you might want to
make, which eventually fall into one of the four categories above.

### Making Changes

The end result of using a `ChangeTrack` object is an array of `FileTextChanges` objects. The `ChangeTrack.with`
function lets you work with a tracker instance elsewhere and passes back the `ChangeTrack` objects.

The core work in generating changes occurs in:

- [`getTextChangesFromChanges`][4]
- [`computeNewText`][5]
- [`getFormattedTextOfNode`][6]

Going from an AST node to text is done by creating a [`printer`][7] in [`getNonformattedText`][8]. The printer
returns an unformatted node, which is then ran through [a formatter][./formatting.md] and the raw string
substitution is done in [`applyChanges`][9].

Changes look like this:

```ts
[{ fileName: "/b.js", textChanges: [{ span: { start: 0, length: 0 }, newText: "// @ts-ignore\n" }] }];
```

### Writing

[`newFileChanges`][3] handles passing the set of

<!-- prettier-ignore-start -->
[0]: https://github.com/microsoft/TypeScript/blob/db9e0079/src/services/textChanges.ts#L303
[0]: <src/services/textChanges.ts - export class ChangeTracker>
[1]: https://github.com/microsoft/TypeScript/blob/db9e0079/src/services/textChanges.ts#L136
[1]: <src/services/textChanges.ts - type Change =>
[2]: https://github.com/microsoft/TypeScript/blob/db9e0079/src/services/textChanges.ts#L1134
[2]: <src/services/textChanges.ts - function createWriter>
[3]: https://github.com/microsoft/TypeScript/blob/db9e0079/src/services/textChanges.ts#L1022
[3]: <src/services/textChanges.ts - function newFileChanges>
[4]: https://github.com/microsoft/TypeScript/blob/db9e0079/src/services/textChanges.ts#L994
[4]: <src/services/textChanges.ts - function getTextChangesFromChanges>
[5]: https://github.com/microsoft/TypeScript/blob/db9e0079/src/services/textChanges.ts#L1035
[5]: <src/services/textChanges.ts - function computeNewText>
[6]: https://github.com/microsoft/TypeScript/blob/db9e0079/src/services/textChanges.ts#L1065
[6]: <src/services/textChanges.ts - function getFormattedTextOfNode>
[7]: https://github.com/microsoft/TypeScript/blob/db9e0079/src/compiler/emitter.ts#L852
[7]: <src/compiler/emitter.ts - function createPrinter>
[8]: https://github.com/microsoft/TypeScript/blob/db9e0079/src/services/textChanges.ts#L1088
[8]: <src/services/textChanges.ts - function getNonformattedText>
[8]: https://github.com/microsoft/TypeScript/blob/db9e0079/src/services/textChanges.ts#L1101
[8]: <src/services/textChanges.ts - function applyChanges>
<!-- prettier-ignore-end -->
