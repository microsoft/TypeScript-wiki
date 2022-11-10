The Definitely Typed tests test new versions of Typescript on every package on Definitely Typed.
They do this two ways:

1. Overnight, [a scheduled run](https://dev.azure.com/definitelytyped/DefinitelyTyped/_build?definitionId=8&_a=summary).
2. On-demand, [triggered by a request to the bot](Triggering-TypeScript-Bot).

The on-demand test runs twice: once on `main` and once on a commit from a PR. It's intended to let a PR author see what *new* errors are introduced by their change.
The overnight test runs on the latest commit on Typescript. It's intended to let the Typescript team know what will break in the next release.

Maintaining the Definitely Typed tests boils down to noticing errors in the overnight run and fixing them.
There are 3 causes of failures:

1. Typescript changed and broke packages in a bad way: File a bug on Typescript.
2. Typescript changed and broke packages in a good way: File a bug, and probably a PR, on the package.
3. The package changed and now breaks, perhaps only on `typescript@next`: File a bug, and maybe a PR, on the package.

For packages on Definitely Typed, don't bother filing a bug.
Nobody reads them anyway.

Instead, if the break is a good one, fix the problem yourself and send a PR.

For Typescript and other repos, while the bugs are being fixed, you can add the package names to [expectedFailures.txt in DefinitelyTyped-tools](https://github.com/microsoft/DefinitelyTyped-tools/blob/master/packages/dtslint-runner/expectedFailures.txt).

### Categories of breaks

1. Trivial re-ordering in `$ExpectType`, which uses textual equality. This is a good break.
2. Out of memory, usually when Typescript changes how much memory it uses. This is almost always a bad break.
3. Complex assignability change. This is usually a good break.
4. Untested types for package written in Javascript. This is usually a bad break, but requires a fix in the package, not Typescript.
3. Those that from afar look like flies.


### Miscellanea

(To be moved to better categories)

- The DT tests test not only the packages on Definitely Typed, but their dependencies. As more packages ship their own types, it's become a valuable way to see what dependencies in the Typescript ecosystem will break.
- The on-demand test is split across four machines; the overnight test is split across two.
- You can sign up to be mailed an alert when the overnight run has a failure.
