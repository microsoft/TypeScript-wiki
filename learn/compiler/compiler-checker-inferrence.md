# Inference within the Checker

There are different routes into the inference engine depending on where the object needs be be inferred (params,
return values, signature etc) but the system for inferring starts with [`function inferTypes`][0].

This function takes a few params:

- `inferences: InferenceInfo[]` - a config object
- `originalSource: Type`
- `originalTarget: Type`
- `priority: InferencePriority = 0`
- `contravariant = false` (see [strict function types][1]))

### Instance var inference

```ts
const a = "Hello World";
```

<!-- prettier-ignore-start -->

[0]: <src/compiler/checker.ts - function inferTypes(>
[1]: https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-6.html#strict-function-types

<!-- prettier-ignore-end -->
