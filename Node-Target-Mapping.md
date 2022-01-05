## Recommended Node TSConfig settings

You can let TypeScript compile as little as possible by knowing what the baseline support 
for ECMAScript features are available in your node version

You can also use https://github.com/tsconfig/bases/ to find `tsconfig.json`s to extend, simplifying your own JSON files to just the options for your project.

To update this file, you can use [node.green](https://node.green) to map to the different options in [microsoft/typescript@src/lib](https://github.com/Microsoft/TypeScript/tree/main/src/lib)

#### Node 16

```json
{
  "compilerOptions": {
    "lib": ["ES2021"],
    "module": "commonjs",
    "target": "ES2021"
  }
}
```

#### Node 14

```json
{
  "compilerOptions": {
    "lib": ["ES2020"],
    "module": "commonjs",
    "target": "ES2020"
  }
}
```

#### Node 12

```json
{
  "compilerOptions": {
    "lib": ["ES2019"],
    "module": "commonjs",
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

Note: Follow [issue #20411](https://github.com/Microsoft/TypeScript/issues/20463) for more information on changes to the es2018 target/lib.

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

