# Frequently Asked Questions

## How can I find out if a type is `number[]`?

```ts
getElementTypeOfArrayType(t) === numberType
```

`getElementTypeOfArrayType` returns undefined if `t` is not an array type.
Use `isArrayType` or `isArrayLikeType` if that's all you need to know.

## How can I delete nodes in a transformer?

Probably you return `undefined` instead of a new or existin node, but look at src/compiler/transformers/ts.ts.
Deleting type annotations is its main job.

