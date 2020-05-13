This is the list of release activities needed for every TypeScript release.
[Additional activities](#additional-activities) are also needed if a new syntax is introduced or a new compiler option gets added.

## Release Candidate Activities

### tslib

* [ ] Add all tslib updates
* [ ] Review next version number

### `@definitelytyped/header-parser` and `retag`

Once `master`'s version is updated, the @definitelytyped packages must be aware of the nightly's new version so that functionality such as ATA continues to work.

* [ ] Update [@definitelytyped/typescript-versions](https://github.com/Microsoft/DefinitelyTyped-tools/tree/master/packages/typescript-versions) to support ***the next version***, and publish new version
    * in other words, if we're releasing TypeScript 3.9, the header parser needs to support `4.0`.
    * To do this, add the new version to the `supported` list.
* [ ] Run [@definitelytyped/retag](https://github.com/Microsoft/DefinitelyTyped-tools/tree/master/packages/retag) to add the tag `ts4.0` to each package.


## Release Activities

### Release

* [ ] [Tag](https://github.com/Microsoft/TypeScript/tags) release branch
* [ ] Update [AUTHORS.md](https://github.com/Microsoft/TypeScript/blob/master/AUTHORS.md) for the release ([script](https://github.com/Microsoft/TypeScript/blob/master/scripts/authors.ts))
* [ ] Draft and publish new [release](https://github.com/Microsoft/TypeScript/releases)
* [ ] Close milestone corresponding to the release

### Wiki

* [ ] Update [What's new in TypeScript](https://github.com/Microsoft/TypeScript/wiki/What%27s-new-in-TypeScript)
* [ ] Update [Breaking Changes](https://github.com/Microsoft/TypeScript/wiki/Breaking-Changes)
* [ ] Update [API Breaking Changes](https://github.com/Microsoft/TypeScript/wiki/API-Breaking-Changes)

### Handbook

* [ ] Add new [Release Notes page](https://github.com/Microsoft/TypeScript-Handbook/tree/master/pages/release%20notes) for the new release
* [ ] Update [Handbook](https://github.com/Microsoft/TypeScript-Handbook) (As needed)

#### npm

* [ ] Publish new version of [typescript](https://www.npmjs.com/package/typescript)
* [ ] Publish new version of [tslib](https://www.npmjs.com/package/tslib) (if needed) by creating a tag

#### Nuget

* [ ] Publish new release to https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/

#### Visual Studio Marketplace

* [ ] Publish VS 2015 and VS 2017 installers under [TypeScript team](https://marketplace.visualstudio.com/search?term=publisher%3A%22TypeScript%20Team%22&target=VS&category=All%20categories&vsVersion=&sortBy=Relevance)

#### TypeScript-Sublime-Plugin

* [ ] Update version of [tsserver](https://github.com/Microsoft/TypeScript-Sublime-Plugin/tree/master/tsserver), test, and tag

#### dtslint and types-publisher

After the release version is published to npm:

* [ ] Update [@definitelytyped/typescript-versions](https://github.com/Microsoft/DefinitelyTyped-tools/tree/master/packages/typescript-versions): move the newly  published version from `supported` to `shipped` (in the example above, that's 3.9), and publish new version of @definitelytyped.
* [ ] Update [dtslint](https://github.com/Microsoft/dtslint) and dts-critic's dependency to the new @definitelytyped/header-parser
* [ ] Update [@definitelytyped/publisher](https://github.com/Microsoft/DefinitelyTyped-tools)'s dependency on dtslint. You may still need to clear caches on Travis, although it usually caches npm packages correctly.

#### Website

* [ ] Update Handbook
* [ ] Add new release notes page
* [ ] Update playground
* [ ] Update version strings in the download banner

## Additional activities

When a new syntax or a new compiler option is introduced, find below the list of additional release activities needed:

### New compiler option added

#### MSBuild tasks and targets

* [ ] Add support for new option in MSBuild tasks and targets (see [handbook](https://github.com/Microsoft/TypeScript-Handbook/blob/master/pages/Compiler%20Options%20in%20MSBuild.md))

#### Handbook

* [ ] Add new option to [Compiler Options](https://github.com/Microsoft/TypeScript-Handbook/blob/master/pages/Compiler%20Options.md)
* [ ] Add new option to [Compiler Options in MSBuild](https://github.com/Microsoft/TypeScript-Handbook/blob/master/pages/Compiler%20Options%20in%20MSBuild.md)

#### SchemaStore

* [ ] Add new option to [tsconfig.json schema](https://github.com/SchemaStore/schemastore/blob/master/src/schemas/json/tsconfig.json)
* [ ] Add new option to [jsconfig.json schema](https://github.com/SchemaStore/schemastore/blob/master/src/schemas/json/jsconfig.json)

### New syntax introduced

#### Colorization

* [ ] Add colorization support in [TypeScript-TmLanguage](https://github.com/Microsoft/TypeScript-TmLanguage)

#### Babel

* [ ] Add parsing support in [Babylon](https://github.com/babel/babel/tree/master/packages/babylon)
* [ ] Add emit support to [babel-plugin-syntax-typescript](https://github.com/babel/babel/tree/master/packages/babel-plugin-syntax-typescript)
* [ ] Update the [TypeScript-Babel-Starter](https://github.com/Microsoft/TypeScript-Babel-Starter#readme) as needed

#### Handbook

* [ ] Add new section for the new feature in the [handbook](https://github.com/Microsoft/TypeScript-Handbook)