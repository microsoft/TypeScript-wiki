If you're a TS maintainer, you can respond to a PR with a comment similar to
```
@typescript-bot test this
```
to trigger a specialized on-demand build on the PR.

The currently recognized commands are:
* [`test this`](https://typescript.visualstudio.com/TypeScript/_build?definitionId=11) - This runs the internal RWC suite against the PR (this takes around 20 minutes). If the tests fail, a diff will be produced and a PR against the internal RWC suite repo will be made with the diff.
* [`run dt`](https://typescript.visualstudio.com/TypeScript/_build?definitionId=23) - The runs the definitely typed linter using the PR TS build sharded across 4 worker containers (this takes around 25 minutes).
* [`run dt slower`](https://typescript.visualstudio.com/TypeScript/_build?definitionId=18) - This is the same as the above, but only on  a single worker (this takes around 90 minutes). This is useful if the results aren't needed promptly and the build queue is busy.
* [`user test this`](https://typescript.visualstudio.com/TypeScript/_build?definitionId=24) - This runs the nightly-tested `user` suite against the PR (this takes around 30 minutes). If this fails, the bot will attempt to open a PR against the triggering PR with the baseline diffs - this will only succeed if the PR is for a branch on `Microsoft/TypeScript`, or if the fork has PRs enabled (so, bit of advice if you plan to use this on your branches: enable PRs on your personal fork).
* [`perf test`](https://typescript.visualstudio.com/TypeScript/_build?definitionId=22) - This queues a build on our perf server using the code from the PR - once started (which will only happen once any currently running build is done), this takes around 40 minutes. The bot should post the results of the perf test run back into the triggering PR once done.
* [`pack this`](https://typescript.visualstudio.com/TypeScript/_build?definitionId=19) - This creates a build which does a build, runs an LKG, runs normal tests, and then packs the result into an installable tarball (which can be downloaded from the build artifacts on the azure pipelines build status page), perfect for installing with `npm i <URL to tarball>` to test with.

A single comment may contain multiple commands, so long as each is prefixed with a call to `@typescript-bot`.

The source of the webhook running the bot is currently available [here](https://github.com/weswigham/typescript-bot-test-triggerer).