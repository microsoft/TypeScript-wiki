# Background

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

# FAQ

## Why do you need to set the patch version on pre-releases?

I think some weirdness around publishing extensions to the Visual Studio marketplace.
To accomodate it and stay reasonable, we just version consistently across npm, NuGet, and VS Marketplace.