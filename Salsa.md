# Salsa

Salsa is the name of the new JavaScript language service powered by TypeScript. 

## How to Enable Salsa ##

### Visual Studio ###

In Visual Studio 2016 preview Salsa is can be enabled by a editing the registry key: <add key>

### VS Code ###

Salsa is enabled by default starting with version 0.10.10.

## Automatic Typing Acquisition
### Overview
The new language service requires TypeScript definition (.d.ts) files to power IntelliSense when types can’t be inferred by the TypeScript compiler. 
For external libraries, most of these .d.ts files can be found in the GitHub repository [Definitely Typed](https://github.com/DefinitelyTyped/DefinitelyTyped). 
Fetching them manually can be tedious, so we created a new feature that infers external library dependencies for your project and then automatically downloads the corresponding TypeScript definition files to enhance IntelliSense for those libraries. 

For example, if you have the dependency "jquery" in your `package.json`, the feature will download the "jquery.d.ts" to `[Your project root]\typings` folder, and use it to enhance IntelliSense during your editing session. 
Note that the feature will also create a `tsd.json` file at your project root path as a registry for acquired typing files, so that next time the downloaded `jquery.d.ts` can be used directly without downloading.

To enable this feature:
Create a `tsconfig.json` or `jsconfig.json` file in your project root.
NOTE: The schema of `tsconfig.json` and `jsconfig.json` is the same, and can be found [here](https://github.com/Microsoft/typescript/wiki/tsconfig.json).
If a `tsconfig.json` file is used, make sure to set the `allowJs` compiler option to `true`, otherwise no `.js` files will be included in the project.
The config file might look like this:
```json
{
    "compilerOptions": {
        "allowJs": true
    },
    "exclude": [
        "typings",
        "node_modules"
    ],
    "typingOptions": {
        "enableAutoDiscovery": true
    }
}
```
Open a `.js` file in VS Code, and you will see a `typings` folder being created with downloaded `.d.ts` files in it.

### Customize the typing experience
There are several `typingOptions` available to decide what typing files to use:
- `enableAutoDiscovery`: a boolean value deciding to automatically infer typing information or not. 
If set to `true`, the language service will look for typing information from the following sources:
    - `dependencies` property in common config files in project root level
      For example, `package.json` and `bower.json`
    - installed node packages in `node_modules` folder, if there is any
    - `.d.ts` files came with installed node module
    - loose JavaScript files that has the name of common libraries
      If the project contains a file named `jquery.js`, the `jquery` typing files will be downloaded.

- `include`: An array of library names whose typing files are needed, regardless of the inferring process.
A sample value looks like: `["jquery", "commander"]`.
This can be used to implicitly add a library when auto discovery fails.

- `exclude`: an array of library names that should not be used, regardless of the inferring process.
If a library name is put in this array, the corresponding `.d.ts` files wouldn't be used during editing session, even if they were already downloaded in the `typings` folder.
This can fix the case when auto discovery falsely used wrong typing information for the project.

### Common issues
The `node_modules` folder can contain thousands of JavaScript files and when this folder is included in the project as a source folder VS Code can run out of memory. 
To avoid this, make sure your `tsconfig.json` or `jsconfig.json` has either an explicit `files` array, or the `node_modules` folder in the `exclude` folder list (not the `exclude` library name list within `typingOptions`).