# Completions


Completions for TypeScript and JavaScript are provided by TypeScript's language service.
When you are in the middle of typing something in an editor (or if you hit <kbd>Ctrl</kbd> + <kbd>Space</kbd> in VSCode), the editor sends a request to the TypeScript language service.
Completions is responsible for answering that request with suggestions to *complete* what you are typing.


## Overview

Most of the implementation lives in the `src/services/completions.ts` file, and there are several steps the implementation goes through to answer a completion request.

The entry point into completions is `getCompletionsAtPosition()`.
As the name suggests, this function takes a `SourceFile` and a `position` as arguments (among other things), and it returns a [`CompletionInfo`](https://github.com/microsoft/TypeScript/blob/404a7d602df9c19d98d49e6a6bf2295e423be676/src/services/types.ts?#L1172-L1191) object with completions for that specific position.

`CompletionInfo` has a few different properties, but we're mainly interested in the `entries: CompletionEntry[]` property, because a [`CompletionEntry`](https://github.com/microsoft/TypeScript/blob/404a7d602df9c19d98d49e6a6bf2295e423be676/src/services/types.ts?#L1220-L1249) encodes the suggestions returned.


### Completion entry

Some `CompletionEntry` properties and what they mean:

* **name**: the name of that completion entry. Usually if the completion is for an identifier/keyword named `foo`, then the name of the entry is also going to be `foo`.
* **insertText**: the text that is going to be inserted in the file, at the completion position, when the user accepts the suggestion corresponding to this completion entry.
`insertText` is optional, and if it is not present, then `name` is the text that is inserted instead.
* **isSnippet**: if this is true, then this completion entry is a snippet, and `insertText` is a snippet text.  
  e.g.:
  A completion snippet for declaring a method `foo`, with a tab stop (`${0}`) in its body:
  ```ts
  {
      isSnippet: true,
      insertText: "foo() { ${0} }",
  }
  ```
  becomes this in VSCode, when accepted (note the cursor position):
  ![Screenshot of vscode with code `class Foo { foo() { | } }` in it.](../../screenshots/snippet-vscode.png)
  For more on snippets, see [Snippets in Visual Studio Code](https://code.visualstudio.com/docs/editor/userdefinedsnippets).
* **replacementSpan**: the span (i.e. a continuous range) of the source file that is going to be *replaced* by the text inserted by this completion. It is optional, so we only need to provide this if we want to override the *default* replacement span for this completion entry. 
* **hasAction**: whether that completion requires additional actions if it is accepted. For instance, a completion might insert variables that need to be imported, so if that completion is accepted, it needs an additional action of inserting import statements.

## Implementation

`getCompletionsAtPosition()` goes through a lot of steps and additional function calls before returning a `CompletionInfo` response.
Roughly the steps are:
1. call `getCompletionData` to gather the data needed to construct a `CompletionInfo`.  
`getCompletionData`'s returns data including a **list of symbols** for things (e.g. variables, properties) we may want to offer for completions. 
2. We call the appropriate function for transforming the completion data into completion info.
The exact function called depends on the the kind of data returned by `getCompletionData`, which can be:
    * `JSDoc`: JSDoc-specific completion data,
    * `Keywords`: keyword completion data,
    * `Data`: general data not falling into the above categories (aka everything else).
    
    If the data is of jsdoc kind, then we call `jsdocCompletionInfo`, if it is keyword data we call `specificKeywordCompletionInfo`.  
    Most importantly, though, when we have the general kind of data, we proceed with **calling `completionInfoFromData`**.
    This is the flow you want to look at most of the time, so let's assume we are following this general flow.
3. `completionInfoFromData` is called with the data we got from `getCompletionData`.  
Mainly, it calls `getCompletionEntriesFromSymbols` to construct completion entries from the symbols obtained in `getCompletionData`.

### `getCompletionData`

Step one is to grab [a `CompletionData`][1] via [`getCompletionData`][2]. This function tries to find a context
token which first looks forwards, and then try find a `contextToken`. This is generally the preceding token to
your cursor, as that tends to be the most important thing when deciding what to show next. This takes into account
things like `x.y` and `y?.y` by diving deeper into preceding identifier.

This dive to find a "responsible" item for a completion request called `node` in the code.

Next it goes through the following checks for a set of completions.
TODO: continue this.

### [`getCompletionEntriesFromSymbols`]((https://github.com/Microsoft/TypeScript/blob/340f81035ff1d753e6a1f0fedc2323d169c86cc6/src/services/completions.ts#L305))

Some completion scenarios require doing special work when transforming a symbol into a completion entry.
That special work is done here, in `getCompletionEntriesFromSymbols`, when we call `createCompletionEntry`.

As an example, let's walk through [class member snippet completions](https://github.com/microsoft/TypeScript/pull/46370), a completion scenario that suggests whole class member declarations (i.e. method and property declarations).
In `createCompletionEntry`, we get the symbol for a class member, say a method `foo`, that we want to offer as a completion. First, we detect that this symbol is for a class member (i.e. method `foo`'s symbol).
Then, to turn that symbol into a completion entry, we have to figure out what the `insertText` for the entry must be.
For method `foo`'s completion entry, we decide the `insertText` is going to be the declaration for method `foo`, something like:
```ts
foo(x: string): number {
    // empty implementation
}
```
So, to get that custom `insertText`, `createCompletionEntry` calls [`getEntryForMemberCompletion`](https://github.com/microsoft/TypeScript/blob/404a7d602df9c19d98d49e6a6bf2295e423be676/src/services/completions.ts#L857).

Another scenario that works similarly is import completions: in `createCompletionEntry`, we call [`getInsertTextAndReplacementSpanForImportCompletion`](https://github.com/microsoft/TypeScript/blob/404a7d602df9c19d98d49e6a6bf2295e423be676/src/services/completions.ts#L1118) to get the custom `insertText` for a completion for importing a symbol, for instance `import { foo } from "foo"`.

## String Literal Completions

E.g. are you inside a string and asking for completions? TS differentiates between reference comments
([triple slash](https://www.typescriptlang.org/docs/handbook/triple-slash-directives.html)):

![../../screenshots/threeslash-refs.png](../../screenshots/threeslash-refs.png)

And strings as a part of the AST. These have a
[few](https://github.com/Microsoft/TypeScript/blob/340f81035ff1d753e6a1f0fedc2323d169c86cc6/src/services/stringCompletions.ts#L103)
different uses:

- They could be path references
- They could be module references
- They could be indexed keys from an object
- They could be parts of a union object

####

<!-- prettier-ignore-start -->
[1]: https://github.com/microsoft/TypeScript/blob/db9e0079/src/services/completions.ts#L1525
[1]: <src/services/completions.ts - interface CompletionData {>
[2]: https://github.com/microsoft/TypeScript/blob/db9e0079/src/services/completions.ts#L1618
[2]: <src/services/completions.ts - function getCompletionData(>
<!-- prettier-ignore-end -->
