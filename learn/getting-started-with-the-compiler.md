This page follows on from [[Learn the Compiler|learn-the-compiler]].

### Getting set up

To get your VS Code env working smoothly, set up the per-user config

- Set up your `.vscode/launch.json` by running: `cp ./vscode/launch.template.json ./vscode/launch.json`
- Set up your `.vscode/settings.json` by running: `cp ./vscode/settings.template.json ./vscode/settings.json`

In the `launch.json` I duplicate the configuration, and change `"${fileBasenameNoExtension}",` to be whatever test
file I am currently working on.

### Learn the debugger

You'll probably spend a good amount of time in it, if this is completely new to you. Here is a video from
[@alloy](https://github.com/alloy) covering all of the usage of the debugger inside VS Code and how it works.

To test it out, open up `src/compiler/checker.ts` find `function checkSourceFileWorker(node: SourceFile) {` and
add a debugger on the first line in the code. If you open up `tests/cases/fourslash/getDeclarationDiagnostics.ts`
and then run your new debugging launch task `Mocha Tests (currently opened test)`. It will switch into the
debugger and hit a breakpoint in your TypeScript.

You'll probably want to add the following to your watch section in VS Code:

- `node.__debugKind`
- `node.__debugGetText()`
- `source.symbol.declarations[0].__debugKind`
- `target.symbol.declarations[0].__debugKind`

This is really useful for keeping track of changing state, and it's pretty often that those are the names of
things you're looking for.

If you want to see what a debugging session looks like with the compiler team, watch [these](https://www.youtube.com/watch?v=3vwO4DwlGYE) [videos](https://www.youtube.com/watch?v=Xq4p5LXaO8Y).

### Getting started

You can learn about the different ways to [[write a test here|systems-testing]] to try and re-produce a bug. It
might be a good idea to look in the recently closed PRs to find something small which adds a test case, then read
the test and the code changes to get used to the flow.
