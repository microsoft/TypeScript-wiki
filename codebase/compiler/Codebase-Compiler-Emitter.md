## Emitter

The emitter is a tree based syntax emitter. It works by going through the TypeScript AST for a program and
emitting source code as it is pipelined.

The emitter itself is "dumb" in the sense that it doesn't contain logic outside of printing whatever AST it is
given. So, it's possible that a bug in emission is actually that the AST isn't set up the way that you'd like it.

### Outfile

Creating a single file which represents many is done by creating a `SyntaxKind.Bundle`. Printing happens in
[`function writeBundle(`][0]. There are `prepends` which I don't understand, and then each sourcefile is is
printed.

### Printer

The printer is a part of the emitter, you create one with [`createPrinter`][1], then start calling [`print`][2]
with an AST node on it. This adds the node via a [pipeline][3]:

```ts
const enum PipelinePhase {
  Notification,
  Substitution,
  Comments,
  SourceMaps,
  Emit,
}
```

With the word to start emitting through the AST in [`pipelineEmitWithHint`][4]. There is a hint option which can
be used to force the emit type.

## Post Processing via Transformers

The process of changing your AST into the expected JS or TS happens the emitter compiler transformers. There is a
full step

Emitting a declaration file is a multi-step process. It goes through the above emitter of its AST, but then _also_
goes through a

<!-- prettier-ignore-start -->
[0]: https://github.com/microsoft/TypeScript/blob/db9e0079/src/compiler/emitter.ts#L1041
[0]: <src/compiler/emitter.ts - function writeBundle>
[1]: https://github.com/microsoft/TypeScript/blob/db9e0079/src/compiler/emitter.ts#L852
[1]: <src/compiler/emitter.ts - function createPrinter>
[2]: https://github.com/microsoft/TypeScript/blob/db9e0079/src/compiler/emitter.ts#L1129
[2]: <src/compiler/emitter.ts - function print(>
[3]: https://github.com/microsoft/TypeScript/blob/db9e0079/src/compiler/emitter.ts#L844
[3]: <src/compiler/emitter.ts - const enum PipelinePhase>
[3]: https://github.com/microsoft/TypeScript/blob/db9e0079/src/compiler/emitter.ts#L1270
[3]: <src/compiler/emitter.ts - function pipelineEmitWithHint(>
<!-- prettier-ignore-end -->
