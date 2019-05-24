## Release activities

This is the list of release activities needed for every TypeScript release.
[Additional activities](#additional-activities) are also needed if a new syntax is introduced or a new compiler option gets added.

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

### types-publisher and definitelytyped-header-parser

**Following the publish**, these tasks must be done in order:

* [ ] Update [definitelytyped-header-parser](https://github.com/Microsoft/definitelytyped-header-parser) to understand ***the next version***, and publish new version
    * in other words, if we're releasing TypeScript 3.2, the header parser needs to be able to parse `3.3`.
* [ ] Update [dtslint](https://github.com/Microsoft/dtslint)'s dependency to the new header-parser
* [ ] Update [types-publisher](https://github.com/Microsoft/types-publisher)'s dependency to the new header-parser
* [ ] Run the tagging script to tag for new version (version of `typescript@next`)

#### npm

* [ ] Publish new version of [typescript](https://www.npmjs.com/package/typescript)
* [ ] Publish new version of [tslib](https://www.npmjs.com/package/tslib) (if needed)

<!-- SUBSUMED BY MARKETPLACE UPLOADS

#### Download Center

* [ ] Upload Visual Studio 2017 installer to https://www.microsoft.com/en-us/download/details.aspx?id=55258
* [ ] Upload Visual Studio 2015 installer to https://www.microsoft.com/en-us/download/details.aspx?id=48593

-->

#### Nuget

* [ ] Publish new release to https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/

#### Visual Studio Marketplace

* [ ] Publish VS 2015 and VS 2017 installers under [TypeScript team](https://marketplace.visualstudio.com/search?term=publisher%3A%22TypeScript%20Team%22&target=VS&category=All%20categories&vsVersion=&sortBy=Relevance)

#### TypeScript-Sublime-Plugin

* [ ] Update version of [tsserver](https://github.com/Microsoft/TypeScript-Sublime-Plugin/tree/master/tsserver), test, and tag

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