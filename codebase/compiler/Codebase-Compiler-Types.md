# Type Hierarchy

Root class: `Type`

### How are properties stored and found on a type?

`checkPropertyAccessExpressionOrQualifiedName`

### getDeclaredTypeOfSymbol vs getTypeOfSymbol

The problem is that symbols can have both types and values associated with them:

```ts
type A = number
const A = "do not do this"
```

And the compiler needs a way to get the type of both the type and the const.
So it uses `getDeclaredTypeOfSymbol` for types and `getTypeOfSymbol[AtLocation]` for values:

```ts
getDeclaredTypeOfSymbol(A) == number
getTypeOfSymbol(A) == string
```

Confusingly, classes (and enums and aliases) declare both a type and a value, so, a tiny bit arbitrarily, the instance side is the type and the static side is the value:

```ts
class C {
   m() { }
   static s() { }
}
getTypeOfSymbol() == { new(): C, s(): void } == typeof C
getDeclaredTypeOfSymbol() == { m(): void } == C
```

This kind of makes sense when you think about that C actually does when executed: it defines a value that is constructable.
This leads to the "deconstructed class" pattern used in tricky situations, for example:

``` ts
interface C {
  m(): void
}
var C: {
  new(): C
  s: void
}
```

Again, it's a tiny bit arbitrary to choose the static side as the value, since ultimately you get a value from calling new C() too.
But the deconstructed class pattern shows that you can get away with writing just a type for the instance side, whereas you must write a value for the static side.

<!-- prettier-ignore-start -->

[1]: <src/compiler/checker.ts - function checkPropertyAccessExpressionOrQualifiedName(

<!-- prettier-ignore-end -->
