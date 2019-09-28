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

Our releases are deployed using Azure Pipelines, you can see [them here](https://dev.azure.com/typescript/TypeScript/_release?_a=releases&view=mine&definitionId=1)