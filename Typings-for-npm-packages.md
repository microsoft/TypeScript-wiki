TypeScript 1.6 has introduced new way of resolving module names that mimics Node module resolution algorithm. As a consequence of that TypeScript compiler currently can load typings that are bundled with npm package. Compiler will try to discover typings for module 'm' using the following set of rules:
- try to load file 'package.json' located in package folder and read the path to typings file as a value of 'typings' field.
- try to load file 'index.d.ts' located in package folder - this file should contain typings for the package

Precise algorithm of module resolution can be found [here](https://github.com/Microsoft/TypeScript/issues/2338)

Typings file should:
- be a .d.ts file
- be an external module
- not have tripleslash references 

Rationale: typings should not bring new compilable items to the set of compiled files otherwise actual implementation files in package can be overwritten during compilation. Also loading typings should not pollute global scope by bringing potentially conflicting entries from different version of the same library