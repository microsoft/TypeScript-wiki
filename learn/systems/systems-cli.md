## CLI

Oddly, the TS Config is probably best described by the CLI which you can find in
`src/compiler/commandLineParser.ts`.

The CLI starts after parsing [`tsc.ts`][0] which triggers [`executeCommandLine`][1]. This function handles going
from an arg list to structured data in the form of [ParsedCommandLine][2].

The actual work occurs in [`parseCommandLineWorker`][] which sets up some variables:

```ts
const options = {} as OptionsBase;
const fileNames: string[] = [];
const errors: Diagnostic[] = [];
```

Then it starts If the letter is a - then start looking forwards to see if the arg is available in [the
optionmap][4]

[0]: https://github.com/microsoft/TypeScript/blob/master/src/tsc/tsc.ts
[1]: https://github.com/microsoft/TypeScript/blob/1bb6ea03/src/tsc/tsc.ts
[2]: https://github.com/microsoft/TypeScript/blob/1bb6ea03/src/compiler/types.ts#L5232
[3]: https://github.com/microsoft/TypeScript/blob/1bb6ea03/src/compiler/commandLineParser.ts#L1192
[4]: https://github.com/microsoft/TypeScript/blob/1bb6ea03/src/compiler/commandLineParser.ts#L222
