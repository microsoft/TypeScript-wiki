### LKG

The version of TypeScript which is published to npm comes from the Last Known Good (LKG) version of TypeScript. The bundled version of the LKG build is committed into the repo inside the [lib](https://github.com/microsoft/TypeScript/tree/master/lib) as is always available. 

The LKG version of TypeScript is the version of the compiler you would use to work on TypeScript inside this repo. 

### Publish to NPM

Publishing to npm will submit the LKG version of TypeScript to npm. This means that to update a deploy you would need to run:

```sh
gulp LKG
npm publish
```

### Official Releases

For all our release-x branches, the node 12 build always produces a tarball based on the branch. Any one of those tarballs can be promoted (from the build UI) and published as a full release, which then triggers `npm publish`es and GitHub releases.

Our workflow is to prep the release branch, bump it's version, do an LKG, then fire off that generated tarball as the release using Azure Pipelines, you can see [them here](https://dev.azure.com/typescript/TypeScript/_release?_a=releases&view=mine&definitionId=1)

