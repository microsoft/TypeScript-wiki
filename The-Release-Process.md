# What Work Gets Done?

TypeScript currently has two pre-releases (the Beta and the Release Candidate), followed by a stable "final" release, followed by any number of patches.

## What gets done before the Beta?

During the beta, new features, breaking changes, and bug fixes are prioritized.
Breaking changes and features are "front-loaded" so that they can get ample testing in the beta and RC.
It also ensures we can roll back changes before there is too much momentum in a release.
We strive to get these in as early as possible so that they are available in [nightly releases](https://www.typescriptlang.org/docs/handbook/nightly-builds.html).

**We strive not to make breaking changes after the Beta release.**

## What gets done before the Release Candidate

Following the beta release, work is done that leads to a Release Candidate (RC).
The RC primarily contains bug fixes and often editor features that were not finished in time for the Beta.
Editor features are okay to go in here because they are relatively easy to back out in time (given dog-fooding, user expectations, the possibility of flagging, lag in editor rollout, and patch releases).

Once the RC goes out, the team begins focus on the next version of TypeScript.

**Very few changes should be expected for a version after the release candidate.**
Changes are cherry-picked on a case-by-case basis for the final release.

## What gets done before a Stable/Final release?

High-priority fixes are applied following the Release Candidate.

By default, new bugs that are not regressions from the upcoming or prior version of TypeScript will be addressed in a new minor version following the final release.

# Release Mechanics

The TypeScript team develops around one central branch: `master`.
This branch is used for nightly builds, and is the source of truth.
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

What happens behind the covers on the engineering team?

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

# FAQ

## Why do you need to set the patch version on pre-releases?

I think some weirdness around publishing extensions to the Visual Studio marketplace.
To accomodate it and stay reasonable, we just version consistently across npm, NuGet, and VS Marketplace.