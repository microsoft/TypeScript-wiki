# How Do I Track Releases?

## Planning and Development

Each release has a corresponding [Iteration Plan](https://github.com/microsoft/TypeScript/issues?q=is%3Aissue+label%3APlanning+%22Iteration+Plan) issue on GitHub.
These iteration plans contain a list of **planned work items** as well as **planned release dates**.

We also keep an updated [Feature Roadmap](https://github.com/Microsoft/TypeScript/wiki/Roadmap) which is typically more accurate in terms of what we release.

## Actual Releases

The TypeScript team also maintains the official [TypeScript Blog](https://devblogs.microsoft.com/typescript/) where releases are announced.

The official [@typescript](twitter.com/typescript/) Twitter account also posts release announcements.

# What Are the Stages of a Release?

TypeScript currently has two pre-releases (the Beta and the Release Candidate), followed by a stable "final" release, followed by any number of patches.

```
                                                         /---> TS 4.1.3 (patch)
TS 4.1.0 Beta ----> TS 4.1.1 RC ------> TS 4.1.2 Stable <
                                                         \----> TS 4.2.0 Beta
```

# How Often Does TypeScript Release?

Releases are planned on a case-by-case basis, but in general you can expect a release around every 3 months.
The breakdown across pre-release versions is typically

* Betas are released about 4 weeks after the prior Stable release.
* Release Candidates are released around 6 weeks after the prior Beta.
* Stable releases are released around 2 weeks after the prior Release Candidate.

# What Work Gets Done?

## What gets done for the Beta?

During the beta, new features, breaking changes, and bug fixes are prioritized.
Breaking changes and features are "front-loaded" so that they can get ample testing in the beta and RC.
It also ensures we can roll back changes before there is too much momentum in a release.
We strive to get these in as early as possible so that they are available in [nightly releases](https://www.typescriptlang.org/docs/handbook/nightly-builds.html).

***We strive not to make breaking changes after the Beta release.***

## What gets done for the Release Candidate

Following the beta release, work is done that leads to a Release Candidate (RC).
The RC primarily contains bug fixes and often editor features that were not finished in time for the Beta.
Editor features are okay to go in here because they are relatively easy to back out in time (given dog-fooding, user expectations, the possibility of flagging, lag in editor rollout, and patch releases).

Once the RC goes out, the team begins focus on the next version of TypeScript.

***Very few changes should be expected for a version after the Release Candidate.***

## What gets done for the Stable/Final release?

High-priority fixes are applied following the Release Candidate for the Stable release.

By default, new bugs that are not regressions from the upcoming or prior version of TypeScript will be addressed in a new minor version following the final release.

## What gets done for patch releases?

Patch releases are periodically pushed out for any of the following:

* High-priority regression fixes
* Performance regression fixes
* Safe fixes to the language service/editing experience

These fixes are typically weighed against the cost (how hard it would be for the team to retroactively apply/release a change), the risk (how much code is changed, how understandable is the fix), as well as how feasible it would be to wait for the next version.

# Which Version Should I Be Using?

We always appreciate early feedback on new versions of the language and editor support!
Depending on how you'd like to test TypeScript and contribute, there are a couple of different options.

## Testing Independently

Despite what the name might imply, [nightly versions](https://www.typescriptlang.org/docs/handbook/nightly-builds.html) are always the preferred way to test out the current state of TypeScript.
While we won't necessarily endorse them for production use, nightlies are always fairly stable and easy to use.

You can download a nightly release via npm (`npm install typescript@next`) and configure your editor support.
If you can't commit to updating your build processes yet, but you write code in Visual Studio Code, you can use the [TypeScript and JavaScript Nightly](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-typescript-next) extension to at least try out new versions of our editor support.

## Testing New Features

If you're willing to try new features, we make Beta releases available.
This is often a good period to start providing feedback on new features, and the closer to the beta release that feedback occurs, the easier we can address it before it officially is added to the language.

The longer you wait after a beta, the less relevant or actionable your feedback can occur.
In those cases, we encourage you to switch to the nightly releases.

## Testing for High-Priority Bug Fixes

If you are extremely limited in your ability to provide feedback, the Release Candidate is the most stable pre-release we provide.
Feedback on the Release Candidate can be addressed, but it is typically limited to high-priority bug fixes, not fundamental changes.

If you intend to provide feedback that will fundamentally affect the design of the language, it should ideally be provided before the RC.
If that is the case, do not wait until the release candidate.
Use nightly releases to test on your own or within your team/organization!

## Staying Up-to-Date

If you can't commit any feedback, just stay up to date on the latest version of TypeScript.
Dedicate a day or two every 3 months or so to perform the upgrade.

_____________
_____________

The following section is targeted more at maintainers and contributors!
If you're simply consuming TypeScript, it is probably not relevant.

# Release Mechanics

The TypeScript team develops around one central branch: `master`.
This branch is used for [nightly builds](https://www.typescriptlang.org/docs/handbook/nightly-builds.html), and is the source of truth.
This central branch is always meant to build cleanly.

# The Typical Release Schedule

The typical release schedule for a version `X.Y` looks like

1. Create X.Y Beta (X.Y.0) Build for Testing
1. **TypeScript X.Y Beta Release**
1. Create X.Y RC (X.Y.1) Build for Testing
1. **TypeScript X.Y RC Release**
1. Create X.Y Final (X.Y.2) Build for Testing
1. **TypeScript X.Y Final Release**
1. Then every week evaluate whether the following needs to be done:
    1. Cherry-pick prioritized new work into the X.Y branch
    1. Release a new patch version

# How Branching Works Around Releases

What does the team have to do to achieve that typical release schedule?

1. Development always just occurs on the `master` branch. This is the default assumption.
1. When we need to create the beta build for TypeScript X.Y, we create a branch called `release-X.Y` and bump the version to `X.Y.0-beta`. On npm, this is published with `--tag beta`.
1. Development for TypeScript X.Y **continues on the `master` branch**.
1. When we need to create an RC build for TypeScript X.Y, we merge the `master` branch into `release-X.Y` and bump the version to `X.Y.1-rc`. On npm, this is published with `--tag rc`.
1. After the RC goes out, the assumption is that all work in `master` will go into TypeScript X.(Y + 1). **Any critical changes will need to be cherry-picked to `release-X.Y`**.
1. When we need to create a build for the stable release version of TypeScript X.Y, we bump the version to `X.Y.2` (with no pre-release version string or tag). On npm, this is published with `--tag latest`.

## The Comment Command Sequence

Much of this process is automated by [Triggering @typescript-bot](https://github.com/microsoft/TypeScript/wiki/Triggering-TypeScript-Bot) to perform tasks, along with a few GitHub actions.
Typically, commands to the bot are given [in the Iteration Plan comments of a release](https://github.com/microsoft/TypeScript/issues?q=is%3Aissue+label%3APlanning+%22Iteration+Plan%22+).
The commands roughly occur in the following order:

1. Readying the Beta
    1. `@typescript-bot create release-X.Y` (create the branch)
    1. In the event that changes need to come in after:
        1. `@typescript-bot sync release-X.Y`
        1. Run [Update LKG](https://github.com/microsoft/TypeScript/actions?query=workflow%3A%22Update+LKG%22) on `release-X.Y`.
1. Readying the RC
    1. `@typescript-bot sync release-X.Y` (sync `master` to `release-X.Y`)
    1. `@typescript-bot bump release-X.Y` (update the version number)
    1. In the event that changes need to come in after:
        1. `@typescript-bot sync release-X.Y`
        1. Run [Update LKG](https://github.com/microsoft/TypeScript/actions?query=workflow%3A%22Update+LKG%22) on `release-X.Y`.
1. Readying the Stable Release
    1. `@typescript-bot bump release-X.Y` (update the version number)
    1. On relevant PRs early on, run `@typescript-bot cherry-pick this to release-X.Y`
    1. On PRs that look like they will be the last cherry-pick: `@typescript-bot cherry-pick this to release-X.Y and LKG`
    1. Run [Update LKG](https://github.com/microsoft/TypeScript/actions?query=workflow%3A%22Update+LKG%22) on `release-X.Y` when necessary.

# Release Tasks

Every publish, especially the Beta, RC, and Stable releases, must undergo a set of release activities.
These release activities are documented [here](https://github.com/microsoft/TypeScript/wiki/Release-Activities).

# Publishing

TODO

# FAQ

## Why do you need to set the patch version on pre-releases?

I think some weirdness around publishing extensions to the Visual Studio marketplace.
To accomodate it and stay reasonable, we just version consistently across npm, NuGet, and VS Marketplace.