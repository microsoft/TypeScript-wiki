## Recommended Node TSConfig settings

You can let TypeScript compile as little as possible by knowing what the baseline support 
for ECMAScript features are available in your node version

You can also use https://github.com/tsconfig/bases/ to find `tsconfig.json`s to extend, simplifying your own JSON files to just the options for your project.

To update this file, you can use [node.green](https://node.green) to map to the different options in [microsoft/typescript@src/lib](https://github.com/Microsoft/TypeScript/tree/main/src/lib)

#### Node 24

```json
{
  "compilerOptions": {
    "lib": ["ES2024"],
    "module": "nodenext",
    "target": "ES2024"
  }
}
```

Note: [`module` is set to `nodenext` to allow `require("esm")`](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-5-8.html#support-for-require-of-ecmascript-modules-in---module-nodenext). After TypeScript 5.9 is released, it is recommended to set it to `node20` instead.

#### Node 22

```json
{
  "compilerOptions": {
    "lib": ["ES2023"],
    "module": "nodenext",
    "target": "ES2023"
  }
}
```

Note: [`module` is set to `nodenext` to allow `require("esm")`](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-5-8.html#support-for-require-of-ecmascript-modules-in---module-nodenext). After TypeScript 5.9 is released, it is recommended to set it to `node20` instead.

#### Node 20

```json
{
  "compilerOptions": {
    "lib": ["ES2023"],
    "module": "nodenext",
    "target": "ES2023"
  }
}
```

Note: [`module` is set to `nodenext` to allow `require("esm")`](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-5-8.html#support-for-require-of-ecmascript-modules-in---module-nodenext). After TypeScript 5.9 is released, it is recommended to set it to `node20` instead.

#### Node 18

```json
{
  "compilerOptions": {
    "lib": ["ES2022"],
    "module": "node16",
    "target": "ES2022"
  }
}
```

#### Node 16

```json
{
  "compilerOptions": {
    "lib": ["ES2021"],
    "module": "node16",
    "target": "ES2021"
  }
}
```

Note: Due to a V8 bug, one rarely-used ES2020 feature was incorrectly implemented in Node <16.3.0 - "spread parameters after optional chaining"; see [issue 46325](https://github.com/microsoft/TypeScript/issues/46325). If you use this feature and need to support versions of Node before 16.3.0, you may need to drop `target` to `ES2019`.

#### Node 14

```json
{
  "compilerOptions": {
    "lib": ["ES2020"],
    "module": "node16",
    "target": "ES2020"
  }
}
```

Note: Due to a V8 bug, one rarely-used ES2020 feature was incorrectly implemented in all releases of Node 14 - "spread parameters after optional chaining"; see [issue 46325](https://github.com/microsoft/TypeScript/issues/46325). If you use this feature, you may need to drop `target` to `ES2019`.

#### Node 12

```json
{
  "compilerOptions": {
    "lib": ["ES2019"],
    "module": "node16",
    "target": "ES2019"
  }
}
```

#### Node 10

```json
{
  "compilerOptions": {
    "lib": ["es2018"],
    "module": "commonjs",
    "target": "es2018"
  }
}
```

#### Node 8

```json
{
  "compilerOptions": {
    "lib": ["es2017"],
    "module": "commonjs",
    "target": "es2017"
  }
}
```

