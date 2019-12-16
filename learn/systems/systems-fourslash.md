# Fourslash

The fourslash tests are an integration level testing suite. By this point they have a very large API surface, and
tend to cover a lot of the "user-facing" aspects of TypeScript. E.g. things which an IDE might have an interest in
knowing.

### How to run one

`gulp runtests` will run all the fourslash tests eventually. Or `gulp runtests -i --tests=[filename]` should speed
things up if you only want to see those specific changes.

### How Fourslash runs a test

Fourslash automatically generates mocha tests based on files you put inside [`/tests/cases/fourslash`][0] the code
for this lives in [`/src/testRunner/fourslashRunner.ts`][1]. This class is instantiated in
[`/src/testRunner/runner.ts`][2].

Fom here the main work all lives in [`/src/harness/fourslash.ts`][3] where we'll be spending the rest of this
section. The initial entry point is [`runFourSlashTest`][4] but the work is in [`runFourSlashTestContent`][5].

This function first creates a virtual fs, uses [`parseTestData`][6] to fill up the virtual fs. `parseTestData`:

- Loops through every line in the test file
- If the line starts with `////` then it starts piping the text into a new string which represents the current
  file
- If the line starts with `//` then check whether it's a special case variable (like `Filename`) - if it's not
  then it will get passed as though it were a TSConfig setting.

This isn't `eval`-ing the code, so the tests under are ignored. Here's an example test file:

```ts
/// <reference path="fourslash.ts" />
// #29038

// @allowJs: true
// @checkJs: true

// @Filename: /node_modules/moment.d.ts
////declare function moment(): void;
////export = moment;

// @Filename: /b.js
////[|moment;|]

goTo.file("/b.js");
verify.importFixAtPosition([
  `import moment from "moment";

moment;`
]);
```

### Formatting

<!-- prettier-ignore-start -->
[0]: https://github.com/microsoft/TypeScript/blob/master/src/testRunner/fourslashRunner.ts
[1]: https://github.com/microsoft/TypeScript/blob/master/tests/cases/fourslash
[2]: https://github.com/microsoft/TypeScript/blob/master/src/testRunner/runner.ts
[3]: https://github.com/microsoft/TypeScript/blob/master/src/harness/fourslashImpl.ts
[4]: <src/harness/fourslashImpl.ts - function runFourSlashTest(>
[5]: <src/harness/fourslashImpl.ts - function runFourSlashTestContent(>
[5]: <src/harness/fourslashImpl.ts - function parseTestData(>
<!-- prettier-ignore-end -->
