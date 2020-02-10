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
[0]: https://github.com/microsoft/TypeScript/blob/1bb6ea03/src/services/textChanges.ts#L232
[1]: https://github.com/microsoft/TypeScript/blob/1bb6ea03/src/services/textChanges.ts#L125
[2]: https://github.com/microsoft/TypeScript/blob/1bb6ea03/src/services/textChanges.ts#L966
[3]: https://github.com/microsoft/TypeScript/blob/1bb6ea03/src/services/textChanges.ts#L864
[4]: https://github.com/microsoft/TypeScript/blob/1bb6ea03/src/services/textChanges.ts#L847
[5]: https://github.com/microsoft/TypeScript/blob/1bb6ea03/src/services/textChanges.ts#L877
[6]: https://github.com/microsoft/TypeScript/blob/1bb6ea03/src/services/textChanges.ts#L905
[7]: https://github.com/microsoft/TypeScript/blob/1bb6ea03/src/compiler/emitter.ts#L820
[8]: https://github.com/microsoft/TypeScript/blob/1bb6ea03/src/services/textChanges.ts#L923
[8]: https://github.com/microsoft/TypeScript/blob/1bb6ea03/src/services/textChanges.ts#L931
<!-- prettier-ignore-end -->
