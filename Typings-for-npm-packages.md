TypeScript 1.6 has introduced new way of resolving module names that mimics the Node.js module resolution algorithm. This means the TypeScript compiler can currently load typings that are bundled with npm packages. The compiler will try to discover typings for module `"foo"` using the following set of rules:

1. Try to load the `package.json` file located in the appropriate package folder (`node_modules/foo/`). If present,read the path to the typings file described in the `"typings"` field. For example, in the following `package.json`, the compiler will resolve the typings at `node_modules/foo/lib/foo.d.ts`

    ```JSON
    {
        "name": "foo",
        "author": "Vandelay Industries",
        "version": "1.0.0",
        "main": "./lib/foo.js",
        "typings": "./lib/foo.d.ts"
    }
    ```

2. Try to load a file named `index.d.ts` located in the package folder (`node_modules/foo/`) - this file should contain typings for the package.

The precise algorithm for module resolution can be found [here](https://github.com/Microsoft/TypeScript/issues/2338)

### What your typings file should

* be a `.d.ts` file
* be an external module
* not have triple-slash references 

The rationale is that **typings should not bring new compatible sources** to the set of compiled files; otherwise source files (i.e. `.ts` files) will be considered by the compiler as part of the user code and will be compiled, and outputs in the package can be overwritten with the resulting `.js` outputs.

Additionally, **loading typings should not pollute global scope** by bringing potentially conflicting entries from different version of the same library. Modules have their own scope, and do not pollute the global namespace, if your typings file is not a module, it will be polluting the user global scope, and will cause conflicts with other packages that depend on your package. Similarly `/// <references ... />` can bring global declarations into the global scope and should be avoided.