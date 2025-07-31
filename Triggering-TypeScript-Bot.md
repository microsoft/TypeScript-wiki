If you're a TS maintainer, you can respond to a PR with a comment similar to
```
@typescript-bot [test command]
```
to trigger a specialized on-demand build on the PR.

The following command triggers a set of the more common on-demand tests:
```
@tyepscript-bot test it
```

This will trigger the `test top400`, `user test this`, `run dt`, `perf test this faster` commands.

The currently recognized commands are:
* [`run dt`](https://typescript.visualstudio.com/TypeScript/_build?definitionId=23) - The runs the definitely typed linter using the PR TS build sharded across 4 worker containers (this takes around 25 minutes).
* [`run dt slower`](https://typescript.visualstudio.com/TypeScript/_build?definitionId=18) - This is the same as the above, but only on  a single worker (this takes around 90 minutes). This is useful if the results aren't needed promptly and the build queue is busy.
* [`user test this`](https://typescript.visualstudio.com/TypeScript/_build?definitionId=47) - This runs the nightly-tested `user` suite against the PR and against main (this takes around 30 minutes). The bot will post a summary comment comparing results from the two.
* [`user test tsserver`](https://typescript.visualstudio.com/TypeScript/_build?definitionId=47) - This runs the nightly-tested `user` suite against the PR and against main (this takes around 30 minutes). The bot will post a summary comment comparing results from the two. This variant also tests tsserver, not just tsc.
* [`user test this slower`](https://typescript.visualstudio.com/TypeScript/_build?definitionId=24) - The older version of the user tests, and run on only a single container, meaning it takes around 1h 30m. The nightly run is run using this build.
* [`test top100`](https://typescript.visualstudio.com/TypeScript/_build?definitionId=47) - This runs the top 100 TypeScript repos on GitHub, by stars, against the PR and against main (this takes around 30 minutes). The bot will post a summary comment comparing results from the two.

  Note that `100` can be replaced with any other number up to 3 digits. For example, `test top200`, `test top50`, or `test top999` will all work.
* [`test tsserver top100`](https://typescript.visualstudio.com/TypeScript/_build?definitionId=47) - This runs the top 100 TypeScript repos on GitHub, by stars, against the PR and against main (this takes around 30 minutes). The bot will post a summary comment comparing results from the two. This variant tests tsserver, not tsc.

  Note that `100` can be replaced with any other number up to 3 digits. For example, `test top200`, `test top50`, or `test top999` will all work.
* [`perf test`](https://typescript.visualstudio.com/TypeScript/_build?definitionId=69) - This queues a build on our perf server using the code from the PR - once started (which will only happen once any currently running build is done), this takes around 25 minutes. The bot should post the results of the perf test run back into the triggering PR once done.
* [`perf test faster`](https://typescript.visualstudio.com/TypeScript/_build?definitionId=69) - This is the same as the above, but only runs tsc tests.
* [`pack this`](https://typescript.visualstudio.com/TypeScript/_build?definitionId=19) - This creates a build which does a build, runs an LKG, runs normal tests, and then packs the result into an installable tarball (which can be downloaded from the build artifacts on the azure pipelines build status page), perfect for installing with `npm i <URL to tarball>` to test with.
* [`cherry-pick this to branchname`](https://typescript.visualstudio.com/TypeScript/_build?definitionId=30) - This launches a task to squash the commits from the PR and then open a new PR that cherry-picks the change into branch `branchname`. This takes about 5 minutes as the build agent needs to clone the input PR. The bot should reply if something goes wrong, or otherwise once the new PR is open.
* [`cherry-pick this to branchname and LKG`](https://typescript.visualstudio.com/TypeScript/_build?definitionId=30) - Same as above, but an LKG commit will be added onto the PR after the squashed cherry-pick commit.
* `run repros` - Triggers inline code repro workflow
* [`autofix this`](https://github.com/microsoft/TypeScript/actions/workflows/accept-baselines-fix-lints.yaml) - Invokes a workflow to accept baselines, fix autofixable lint errors, and format code, then commit the result to the PR's branch. This only works on branches of the main repo, not on forks, at present.

In addition, there are a small suite of commands which work in _any_ comment and relate to release management.
You can see how these are typically used in our documented [comment command sequence](https://github.com/microsoft/TypeScript/wiki/TypeScript's-Release-Process#the-comment-command-sequence):

* [`create release-X.Y`](https://github.com/microsoft/TypeScript/actions/workflows/new-release-branch.yaml) This makes a `release-X.Y` branch (replace `X.Y` with your desired version) with the `package.json` version set to `X.Y.0-beta`, the `corePublic` `versionMajorMinor` set to `X.Y`, and the full `ts.version` string set to `X.Y.0-beta`, and updates the accompanying baselines. An LKG is then performed. This new branch is directly pushed to `microsoft/TypeScript`. In short, this fully sets up a new release branch to be ready to publish a beta.
* [`bump release-X.Y`](https://github.com/microsoft/TypeScript/actions/workflows/set-version.yaml) This bumps the version (`0-beta` -> `1-rc` -> `2` -> `3` and so on) on the specified branch and captures a new LKG, essentially preparing the branch for a new release.
* [`sync release-X.Y`](https://github.com/microsoft/TypeScript/actions/workflows/sync-branch.yaml) This merges `master` into the specified branch; this is useful for syncing the branch with `master` in the period between the beta and rc.

A single comment may contain multiple commands, so long as each is prefixed with a call to `@typescript-bot`.

The source of the webhook running the bot is currently available [here](https://github.com/microsoft/typescript-bot-test-triggerer).

Here is the usual invocation of all the useful bot commands at once:

```ts
@typescript-bot test top100
@typescript-bot test tsserver top100
@typescript-bot user test this
@typescript-bot user test tsserver
@typescript-bot run dt
@typescript-bot perf test this
@typescript-bot pack this
```

You can [put this into a saved reply](https://github.com/settings/replies) so it's easily accessible.
