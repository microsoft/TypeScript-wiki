## Recommended Node TSConfig settings

You can let TypeScript transpile as little as possible by knowing what the baseline support 
for ECMAScript features are available in your node version. 

To update this file, you can use [node.green](https://node.green) to map to the different options in [microsoft/typescript@src/lib](https://github.com/Microsoft/TypeScript/tree/master/src/lib)

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

