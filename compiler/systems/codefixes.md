### How do Codefixes work

A codefix is a signal from TypeScript to an editor that it's possible for TypeScript to provide an automated fix
for some particular part of a codebase.

Codefixes are implemented in a way that always requires there to be an error code, which means they bubble through
TypeScript's internals initially from a compiler error.

### Codefix vs Refactor

Code fixes have an associated error code, and can be skipped cheaply when trying to figure out if it's applicable.
A refactor on the other hand does not come from an error code and is therefore somewhat always available, and are
more expensive to check for.

### How are they used?

The code fix assertions come in from comes in from the language service, via [`getCodeFixesAtPosition`][1], this
says here's a file and a selection range for their selected text and any potential compiler error codes that touch
section.

The language service then reaches into [the codeFixProvider][2] via [`getFixes`][3], this delegates its work to
[`getCodeActions`][4] which is a function which each codefix provides.

These are returned to the IDE in the form of an object with:

```ts
interface CodeAction {
  /** Description of the code action to display in the UI of the editor */
  description: string;
  /** Text changes to apply to each file as part of the code action */
  changes: FileTextChanges[];
  /**
   * If the user accepts the code fix, the editor should send the action back in a `applyAction` request.
   * This allows the language service to have side effects (e.g. installing dependencies) upon a code fix.
   */
  commands?: CodeActionCommand[];

  /** Short name to identify the fix, for use by telemetry. */
  fixName: string;
  /**
   * If present, one may call 'getCombinedCodeFix' with this fixId.
   * This may be omitted to indicate that the code fix can't be applied in a group.
   */
  fixId?: {};
  fixAllDescription?: string;
}
```

An example of one is:

```ts
const codefix = {
  description: `Import 'moment' from module "moment"`,
  fixAllDescription: "Add all missing imports",
  fixId: "fixMissingImport",
  fixName: "import"
};
```

### Testing a code fix

You can use fourslash to set up a project that emulates the sort of before and after experience from your codefix

```ts
/// <reference path="fourslash.ts" />
// #29038

// @allowJs: true
// @checkJs: true
// @esModuleInterop: true
// @moduleResolution: node

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

### Example codefix PRs

These are some reference PRs to look at

- https://github.com/microsoft/TypeScript/pull/23711
- https://github.com/microsoft/TypeScript/pull/32281

<!-- prettier-ignore-start -->
[1]: <src/services/services.ts - function getCodeFixesAtPosition>
[2]: src/services/codeFixProvider.ts
[3]: <src/services/services.ts - export function getFixes>
[4]: <src/services/codeFixProvider.ts - export function getFixes>
<!-- prettier-ignore-end -->
