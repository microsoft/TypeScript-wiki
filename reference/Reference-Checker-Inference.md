
[0]: https://github.com/microsoft/TypeScript/blob/db9e0079/src/compiler/checker.ts#L21772# Type Inference

TypeScript has a number of related techniques which together are
called type inference: places where a type is discovered from
inspecting values instead of a type annotation. This document
covers them all in one place even though they're all fairly different.

One thing that that is true of all type inference in TypeScript:
type inference is a separate step that happens before checking. The
checker will infer a type for a location; then it will check the type
in the normal way, as if the type had been explicitly written. This
results in redundant checking when the type inference is simple.

None of these techniques are Hindley-Milner type inference. Instead,
TypeScript adds a few ad-hoc inference techniques to its normal
type-checking. The result is a system that can infer from many useful
locations, but nowhere near all of them.

## Initialiser inference

The simplest kind of inference is from initialisers. This inference is
so simple that I don't believe it has been given a separate name until
now.

You can see this anywhere a variable, parameter or property has an
initialiser:

```ts
let x = 123
function f(x = 123) {
}
class C {
  x = 123
}
```

Remember, inference precedes checking, so checking `let x = 123`
looks like this:

1. Look for the type of `x`.
2. There is no annotation, so use the (widened) type of the initialiser: `number`.
3. Check that the initialiser's type `123` is assignable to `number`.

## Contextual typing

Contextual typing looks upward in the tree for a type based on a type
annotation. This is unlike initialiser inference, which looks at a *sibling*
node for a type based on a *value*. For example, in

```ts
const f: Callback = (a,b) => a.length + b
```

The parameters `a` and `b` are contextually typed by the type
`Callback`. The checker discovers this by looking at the parent nodes
of `a` and `b` until it finds a type annotation on a variable declaration.

In fact, contextual typing only applies to two kinds of things:
parameters and literals (including JSX literals). But it may find a type in a variety of places.
Here are 3 typical ones:

1. A type annotation on a declaration:

```ts
type Config = { before(data: string): void }
const cfg: Config = {
  before(x) {
    console.log(x.length)
  }
}
```

2. The left-hand side of an assignment:

```ts
let steps: ('up' | 'down' | 'left' | 'right')[] = ['up', 'up', 'down', 'down']
steps = ['down']
```

3. An argument in a function call:

```ts
declare function setup(register: (name: string, age: number) => void): void
setup((name, age) => console.log(name, age))
```

The basic mechanism of contextual typing is a search for a type
annotation. Once a type annotation is found, contextual typing walks
down through the *type* by reversing the path it walked up through the
*tree*.

Aside: In example (2), contextual typing gives `'down'` the
*non-widening* type `'down'`; it would otherwise have the type
`string`. That means `['down']` will have the type `'down'[]`, which
is assignable to `steps`. So contextual typing lets programmers avoid
writing `['down' as 'down']` in some cases.

### Walkthrough

Let's walk through example (1).

1. During normal check of the tree,
   `checkFunctionExpressionOrObjectLiteralMethod` is called on
   `before`.
2. This calls `getApparentTypeofContextualType` (after a few
   intermediate functions), which
   recursively looks for the contextual type of `before`'s parent.
3. The parent is an object literal, which recursively looks for the
   contextual type of the object literal's parent.
4. The parent is a variable declaration with a type annotation `Config`.
   This is the contextual type of the object literal.
5. Next we look inside `Config` for a property named `before`. Since's
   `Config.before`'s type is a signature, that signature is the
   contextual type of `before`.
6. Finally, `assignContextualParameterTypes` assigns a type for `x` from
   `Config.before`'s first parameter.

Note that if you have type annotations on some parameters already,
`assignContextualParameterTypes` will skip those parameters.

Contextually typing `(name, age) => ...` in (3) works substantially
that same. When the search reaches `getContextualType`, instead of a
variable declaration, the parent is a call expression. The contextual
type of a call expression is the type of the callee, `setup` in this
case. Now, as before, we look inside `setup`'s type: `(name, age) =>
...` is the first argument, so its contextual type is from the first
parameter of `setup`, `register`. `assignmentContextualParameterTypes`
works for `name` and `age` as in (1).

## Type Parameter Inference

Type parameter inference is quite different from the other two
techniques. It still infers **types** based on provided **values**,
but the inferred types don't replace a type annotation. Instead
they're provided as type arguments to a function, which results in
instantiating a generic function with some specific type. For example:

```ts
declare function setup<T>(config: { initial(): T }): T
setup({ initial() { return "last" } })
```

First checks `{ initial() { return "last" } }` to get `{ initial():
string }`. By matching `T` in `{ initial(): T }` with `string` in `{
initial(): string }`, it infers that `T` is `string`, making the
second line the same as if the author had written:

```ts
setup<string>({ initial() { return "last" } })
```

Meaning that the compiler then checks that
`{ initial() { return "last" } }` is assignable to
`{ initial(): string }`.

### Walkthrough

Type parameter inference starts off in `inferTypeArguments`, where
the first step in type parameter inference is to get the type of all
the arguments to the function whose parameters are being inferred. In
the above example, the checker says that the type of
`{ initial() { return "last" } }` is `{ initial(): string }`. This
type is called the **source** type, since it is the source of
inferences. It's matched with the parameter type `{ initial(): T }`.
This is the **target** type -- it contains type parameters which are
the target of the process.

Type parameter inference is a pairwise walk of the two types, looking
for type parameters in the target, matching them to corresponding
types in the source. The type is walked structurally sort of like a tree
is elsewhere in the compiler.

1. `inferTypes` gets called on each source/target pair with
   argument=source/parameter=target. There's only one pair here:
   `{ initial(): string }` and `{ initial(): T }`.
2. Since both sides are object types, `inferFromProperties` looks
   through each property of the target and looks for a match in the
   source. In this case both have the property `initial`.
3. `initial`'s type is a signature on both sides
   (`() => T/() => string`), so inference goes to `inferFromSignature`, which
   recursively infers from the return type.
4. Now the source/target pair is `T/string`. Since the source is a
   lone type parameter, we add `string` to the list of candidates for
   `T`.

Once all the parameters have had `inferTypes` called on them,
`getInferredTypes` condenses each candidate array to a single type,
via `getUnionType` in this case. `T`'s candidates array is `[string]`,
so `getUnionType` immediately returns `string`.

### Other considerations

#### Method of Combining Candidate Arrays

Only inference to return types, `keyof T` and mapped type constraints
(which are usually `keyof` too) produce a union. These are all
contravariant inference locations. All other locations
call the custom code `getCommonSupertype`, which more or less does
what it says. Note that object types are always unioned together
first, regardless of inference position.

#### Interference Between Contextual Typing and Type Parameter Inference

Type parameter inference actually operates in two passes. The first
pass skips arguments that have contextually typed expressions so that
if good inferences are found from other arguments, contextual typing
can provide types to parameters of function expressions, which in turn
may produce better return types. Then the second pass proceeds with
all arguments.

#### Inference Priorities

Different positions have different inference priorities; when the type
walk finds a candidate at a higher priority position than existing
candidates, it throws away the existing candidates and starts over
with the higher-priority candidate. For example, a lone type variable
has the highest priority, but a type variable found inside a return type
has one of the lowest priorities.

Priorities have two important limitations:
first, they are defined ad-hoc, based on heuristics developed by
observing bad type inferences and trying to fix them. Second, throwing away
low-priority inferences is faster, but will miss some inferences
compared to integrating all priorities in some way.

#### Contravariant Candidates

Certain candidates are inferred contravariantly, such as parameters of
callbacks. This is a separate system from inference priorities;
contravariant candidates are even higher priority.

#### Reverse Mapped Types

A reverse mapped type is a mapped type that is constructed during
inference, and it requires information obtained from inference, but is
not a central part of inference. A reverse mapped type is constructed when
the target is a mapped type and the source is an object type. It
allows a inference to apply to every member of an object type:

```ts
type Box<T> = { ref: T }
type Boxed<T> = { [K in keyof T]: Box<T[K]> }
declare function unbox<T>(boxed: Boxed<T>): T;
unbox({ a: { ref: 1 }, m: { ref: "1" } }) // returns { a: number, m: string }
```

Reverse mapped types are normal types just like conditional types,
index types, mapped types, etc. The difference is that they have no
explicit syntax to construct them.

  <!-- prettier-ignore-start -->

[0]: <src/compiler/checker.ts - function inferTypes(>
[1]: https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-6.html#strict-function-types

  <!-- prettier-ignore-end -->
