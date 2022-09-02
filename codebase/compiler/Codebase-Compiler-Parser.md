
[0]: https://github.com/microsoft/TypeScript/blob/db9e0079/src/compiler/program.ts#L1926# Parser

At a measly 8k lines long, the Parser is responsible for controlling a scanner (or two) and turning the output
tokens from the scanner into an AST as the canonical representation of the source file.

## JSDoc

## Context

Because the parser itself is effectively a state machine which creates nodes from scanning text there is some
reasonable dancing

<!-- prettier-ignore-start -->

[0]: <src/compiler/program.ts - function getDiagnosticsProducingTypeChecker>
[4]: GLOSSARY.md#statements

<!-- prettier-ignore-end -->

`
