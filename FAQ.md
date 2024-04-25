# FAQ (For Issue Filers)

FAQ Update 2024: The FAQ now attempts to only address content from people who end up filing GitHub issues.

This is not intended to be a representative FAQ for TypeScript *in general*.

## Common Feature Requests

### Existing Common Requests

Listed here (with some synonyms) for easier Ctrl-F-ing

 * Nominal types (tagged, branded): [#202](https://github.com/microsoft/TypeScript/issues/202)
 * Negated types (not, exclusion, exclude, remove): [#4196](https://github.com/microsoft/TypeScript/issues/4196)
 * Exact types (sealed, final, closed, unopen): [#12936](https://github.com/microsoft/TypeScript/issues/12936)

## Behavior That Looks Wrong (And Arguably Is) But Is Currently Working As Intended

 * Method and function signatures behave differently, specifically that narrower argument types are unsoundly allowed in subtypes of methods, but not functions. See "Why Method Bivariance?" on this page

## Pre-Declined Feature Rquests

### New Utility Types

While `lib.d.ts` has some built-in types such as `Pick`, `Omit`, `Exclude`, `ReturnType`, etc., we are not accepting suggestions for adding new utility types.

[Experience has taught us](https://github.com/microsoft/TypeScript/issues/30825) that defining utility types opens up a huge range of disagreement about very specific aspects of how these utility types should work, and once we ship a utility type, it's nearly always impossible to change it without causing many subtle breaks in user code.

### Add a Key Constraint to `Omit`

`Omit`s lack of key constraint is intentional. Many use cases for this type do not obey that constraint, e.g.:
```ts
type MySpread<T1, T2> = T2 & Omit<T1, keyof T2>;
type X = MySpread<{ a: string, b: number}, { b: string, c: boolean }>;
let x: X = { a: "", b: "", c: true };
```

You can write a user-space `Omit` type if you'd like to constrain the key.

We also recommend using these definitions of a user-side `Pick` and `Omit` if desired:
```ts
type Pick_NewAndImproved<T, K extends keyof T> = {
  [P in keyof T as K & P]: T[P];
};

// Optional: Add 'extends keyof T' constraint to K
type Omit_NewAndImproved<T, K> = {
  [P in keyof T as Exclude<P, K & keyof any>]: T[P]; }
}
```

### Module Specifier Rewriting

It's explicitly out of scope for TypeScript to modify module specifiers as they appear in emitted JS, e.g. if you write

```ts
import x from "some/path";
```

the output specifier *will always be* `"some/path"` regardless of your tsconfig settings.

This includes things like changing file extensions, changing `paths` lookups to their resolutions, changing absolute paths to relative paths, changing relative paths to absolute paths, changing sub-module specifiers to something else, and so on. The string in the import path is the string in the emitted JavaScript, no exceptions.

Instead of trying to get TypeScript to change the path during emit, the correct approach is to write the specifier you want to be in the output, and adjust your configuration until that specifier resolves (in type-land) to the path you want it to.

See also:

 * [Module documentation](https://www.typescriptlang.org/docs/handbook/modules/theory.html#module-specifiers-are-not-transformed)
 * [This comment](https://github.com/microsoft/TypeScript/issues/49083#issuecomment-1435399267)

### Additional Logic in `noUncheckedIndexedAccess`

`noUncheckedIndexedAccess` is intended to prevent all accidental out-of-bounds access on arrays.

Because array mutation *could* occur at any time, it doesn't make any exceptions for things like `length` checks.

In order to ensure that the flag doesn't have any "gaps", requests to change the logic to produce `T` instead of `T | undefined` will not be accepted.

### `throws` / Checked Exceptions / Typed Exceptions

[See this comment](https://github.com/microsoft/TypeScript/issues/13219#issuecomment-1515037604)

## Common Misconceptions

Note: Section titles here state the *true* version of the fact.

### Primitives are `{ }`, and `{ }` Doesn't Mean `object`

The type `{ }` refers to any (non-null/undefined) value with zero or more properties.

Primitive values, like strings, do have properties. For example, `"hello world".length` is a valid property access, because strings have a `length` property. Therefore, a `string` is a valid `{ }`: it is not null or undefined, and has zero or more properties.

The type that refers to values which have `Object` in their prototype chain is `object`. `{ }` is not a synonym for `object`.

### `{ }` Does Not Refer to Objects With No Properties

Because TypeScript doesn't have sealed/closed types, there's no type which refers to values with zero properties.

Certain [lint rules](https://github.com/typescript-eslint/typescript-eslint/issues/8700) ban using `{}`; we do not recommend this rule and we don't design the language around misguided lint rules. The correct value to use for "any non-null non-undefined value" is `{ }`, other suggested types like `Record<string, never>` are not particularly coherent and shouldn't be used instead. We recommend disabling any lint rule that tries to prevent you from using `{ }`, because (unlike `String` or `Number`) it's a valid type that does occur in normal usage of TypeScript.

### Evolving `let` and Evolving Arrays Aren't `any`

Evolving `let` and evolving arrays are intentional (see PR #11263) and shouldn't be errors under `noImplicitAny`. They do not act like `any`.

These features exist to ensure that these programs have equivalent behavior:
```ts
let x;
if (cond) {
  x = y;
} else {
  x = z;
}
doSomethingWith(x); // <- first read
```
and the identical program:
```ts
let x = cond ? y : z;
doSomethingWith(x);
```
Even those these appear as `any` in tooltips (see #54414), these don't have any of the problems associated with `any`. Allowing an assignment prior to the first read is no different from initialization and should be treated the same as initialization (and is).

### (Indirect) Excess Properties Are OK

Object types in TypeScript aren't "sealed" / "closed" / "final". In other words, if you have a variable of *type* `{ a: string }`, it's possible that the variable points to a *value* like `{ a: "hello", b: 42 }`.

When you're directly creating an object literal, TypeScript uses "excess property checks" to detect likely problems:
```ts
interface Dimensions {
    width: number;
    height: number;
    depth?: number;
}

const p: Dimensions = {
    width: 32,
    height: 14,
    depht: 11 // <-- typo!!
}
```
However, this code is still legal:
```ts
const p = {
    width: 32,
    height: 14,
    depht: 11 // <-- fine
};
console.log(p.depht); // yep, it's there
const q: Dimensions = p; // also fine
```

This also means that `Object.keys` should (and does) return `string[]`, not `(keyof T)[]`. See also [this StackOverflow post](https://stackoverflow.com/questions/55012174/why-doesnt-object-keys-return-a-keyof-type-in-typescript)

See also suggestion #12936

### Parameter Contravariance is Correct

Let's say you write an interface
```ts
interface CanCheck {
    checkThing: (x: string) => boolean;
}
```
and implement it with an object:
```ts
const obj = {
    checkThing: (sn: string | number) => {
        return true;
    }
}
obj satisfies CanCheck; // OK
```

A common confusion is to say that since `string | number` is a bigger type than `string`, this program should be rejected, since it means a number might appear where a string is expected. This reasoning is incorrect; even though a number can be passed to `obj.checkThing`, that cannot create the situation where a `number` is present somewhere where *only* a `string` is expected.

Another common confusion is to claim that the opposite program should be accepted:
```ts
interface CanCheck {
    checkThing: (x: string | number) => boolean;
}
const obj = {
    checkThing: (s: string) => {
        return true;
    }
}
obj satisfies CanCheck; // Alleged: should be OK
```
This is wrong. If `obj` is a `CanCheck`, then `obj.checkThing(42)` is legal, and `42` would appear in `s`, which is only allowed to be a `string`.

Another common confusion is, in response, to say something like

> But a function that takes a string *is* a function that takes a string or number!

This is very, very easy to get backwards. After all, in common parlance, a "carnivore" is someone who eats meat. A person who eats beef would seem to qualify. Yet nearly every carnivore human does *not* eat human meat -- the predicate is not universal over all inputs.

The correct way to phrase this is to insert the necessary qualifiers to the proposition

> A function that can take **any** string is(?) a function that can take **any** string or number

In this phrasing, the flaw is more apparent: Because the function *doesn't* take numbers, it doesn't match the criteria.

This logic also applies equally to methods of classes that implement interfaces; no different behavior is required or justified here.

### Parameter Arity Variance is Correct

> I wrote some code like this and expected an error:
> ```ts
> function handler(arg: string) {
>     // ....
> }
>
> function doSomething(callback: (arg1: string, arg2: number) => void) {
>     callback('hello', 42);
> }
>
> // Expected error because 'doSomething' wants a callback of
> // 2 parameters, but 'handler' only accepts 1
> doSomething(handler);
> ```

This is the expected and desired behavior.

Let's consider another program first:
```ts
let items = [1, 2, 3];
items.forEach(arg => console.log(arg));
```

This is isomorphic to the example that "wanted" an error.
At runtime, `forEach` invokes the given callback with three arguments (value, index, array), but most of the time the callback only uses one or two of the arguments.
This is a very common JavaScript pattern and it would be burdensome to have to explicitly declare unused parameters.

If this *were* an error, it's not even clear how you would fix it! Adding the extra parameters is likely to run afoul of your linter:
```ts
let items = [1, 2, 3];
// Error: Unused variables 'i', 'arr'
items.forEach((arg, i, arr) => console.log(arg));
```
JavaScript doesn't have a "discard" binding method, so you'd either end up with lint suppressions, or ugly and useless parameters:
```ts
// No one wants to write this sad code:
items.forEach((arg, _1, _2) => console.log(arg));
```

> But `forEach` should just mark its parameters as optional!
> e.g. `forEach(callback: (element?: T, index?: number, array?: T[]))`

This is *not* what an optional callback parameter means.
Function signatures are always read from the *caller's* perspective.
If `forEach` declared that its callback parameters were optional, the meaning of that is "`forEach` **might call the callback with 0 arguments**".

The meaning of an optional callback parameter is *this*:
```ts
// Invoke the provided function with 0 or 1 argument
function maybeCallWithArg(callback: (x?: number) => void) {
    if (Math.random() > 0.5) {
        callback();
    } else {
        callback(42);
    }
}
```
`forEach` *always* provides all three arguments to its callback.
You don't have to check for the `index` argument to be `undefined` - it's always there; it's not optional.

There is currently not a way in TypeScript to indicate that a callback parameter *must* be present.
Note that this sort of enforcement wouldn't ever directly fix a bug.
In other words, in a hypothetical world where `forEach` callbacks were required to accept a minimum of one argument, you'd have this code:
```ts
[1, 2, 3].forEach(() => console.log("just counting"));
             //   ~~ Error, not enough arguments?
```
which would be "fixed", but *not made any more correct*, by adding a parameter:
```ts
[1, 2, 3].forEach(x => console.log("just counting"));
               // OK, but doesn't do anything different at all
```

### `void` and `undefined` are Different

`void` is not an alias for `undefined`.

See [this StackOverflow answer](https://stackoverflow.com/a/58885486)

See also #42709

### `Exclude` Isn't Type Negation

`Exclude<T, U>` isn't the same as `T & not U`. TypeScript does not yet have the notion of a negated type; see #4196.

[As mentioned in the documentation](https://www.typescriptlang.org/docs/handbook/utility-types.html#excludeuniontype-excludedmembers), `Exclude` is a type alias whose *only* effect is to filter unions. This behavior comes from its [distributivity](https://www.typescriptlang.org/docs/handbook/2/conditional-types.html#distributive-conditional-types). It isn't a true "built-in" type that performs type negation.

This means that if the input type isn't a union, nothing will happen. This includes types that you might think of as being "infinite unions", like `string` or `number` - they are not union types.

For example, `Exclude<string, "hello">` just means `string`. It doesn't mean "any `string` except `"hello"`", because `string` is not a union, and thus no filtering occurs.

The same is true for numbers; `Exclude<number, 0>` is `number` because `number` is not a union.

### `as` is the Casting Operator, So it Casts

The `as` operator (and its other syntax, `<T>expr`) is in the language *for the purpose* of downcasting, e.g. telling TypeScript that an `Animal` is actually a `Cat`. It's very easy to introduce runtime errors in your program via silencing a type error with a downcast, so care should be taken when using it.

For convenience, this operator also works for upcasting, which is a much less frequently needed operation (usually `satisfies` is better).

### The ECMAScript Spec is Descriptive, not Normative

The ECMAScript spec defines the behavior of JavaScript runtime semantics. This means that even *clearly buggy* code, e.g.:
```ts
const output = "hello, world".substr("world");
```
has *a* defined behavior.

The purpose of TypeScript isn't to tell you when you've somehow managed to reach outside the bounds of the ES Spec (a nearly impossible feat), nor to only tell you when you've done something that will raise a runtime exception (something that most people agree doesn't happen nearly often enough in JS). TypeScript defines a *normative* set of behaviors that we think are generally "good" JS - fewer (but not necessarily zero) implicit coercions, fewer property accesses that result in `undefined`, fewer exceptions, fewer `NaN`s, and so on. This does mean that some behavior is on the borderline of "good" vs "not so good", and there are judgement calls involved when it comes to what TypeScript thinks is OK or not.

See also [this Stackoverflow Post](https://stackoverflow.com/a/41750391/)

### `exclude` in `tsconfig.json` Only Filters `include`

[As mentioned in the docs](https://www.typescriptlang.org/tsconfig#exclude), the `exclude` tsconfig option *only* filters the list of files that `include` picks up. It does not do anything else. Its only purpose is to change which files `include` produces, and doesn't change the behavior of any other processes. The only thing you can use `exclude` for is to have `include` not process a file; if the file is part of your program for a different reason, it will still be there.

`exclude` has no effect on other ways a file might be included:

* Module resolution
* The `files` list
* `/// <reference` directives
* Anything else that isn't `include`

You can run `tsc --explainFiles` to see why a file was included.

### The "lib" in `skipLibCheck` Refers To `.d.ts` Files

The "lib" in "`skipLibCheck`" refers to any `.d.ts` file.

In other words, there is no distinction made between `.d.ts` files which are "yours" (say, in your `files` list) and "not yours" (say, in `node_modules/@types`).

In general it's not correct to have a `.d.ts` file be a user-authored input to your program - even if a file only contains types, it should still be `.ts` so that it *produces* a `.d.ts` in the output for downstream consumers to use.

## Non-Invariants

### Circularity Errors *May* Occur In The Presence of Circularities

During the depth-first process of typechecking, TypeScript may encounter an apparent circularity in logic, e.g. it determines that `X` should be assignable to `Y` if `X` is assignable to `Y`. When this happens, you'll see an error:

> 'foo' implicitly has type 'any' because it does not have a type annotation and is referenced directly or indirectly in its own initializer

Due to the complexity of the checking process and caching, it's sometimes possible for a circularity error to occur in some situations but not others. If a codebase has a circularity in checking, that error *may* be issued, but it's also possible that you may be able to cause the error to go away by pre-doing part of the cycle elsewhere.

In the interests of not arbitrarily breaking people, we're not accepting PRs to *add* more circularity errors.

If you have a working PR that *removes* circularity errors without adverse side effects, we can review them.

A very instructive deeper discussion can be read at [#45213](https://github.com/microsoft/TypeScript/issues/45213).

### Comment Preservation Not Guaranteed

As a trade-off to make parsing more efficient, TypeScript's emitter *may* not emit every comment in the original source.

If your scenario requires every comment, or some particular form of comment, to be preserved, please use an alternate TS-to-JS transpiler.

### Structural vs Instantiation-Based Inference

Consider a function call where TypeScript must perform type inference:
```ts
type Box<T> = { contents: T };
declare function unbox<T>(arg: Box<T>): T;

function foo(x: Box<string>) {
    const a = unbox(x); // What is T?
}
```

There are two different approaches for inferring `T` you can use here:
 * Option 1 (structural): Determine the structure of `Box<string>`, see that it has a `{ value: string }` property, look at each property in `Box<T>` seeing if it uses `T` in some position, notice that there's a `value: string` coming from `x` that corresponds to `value: T` in `Box<T>`, therefore `T = string`
 * Option 2 (instantiation-based): Notice that `Box<string>` is an instantiation of `Box<T>`, so `T = string`

As one might expect, Option 2 is *much* faster, and is also equally correct. As such, TypeScript generally prefers this instantiation-based inference whenever possible.

However, there are cases where instantiation and structural inference can produce different results.

For example if `Box<T>` doesn't actually *use* `T`, then a structurally-based inference will find no occurences of `T` and infer `T = never`. *But* since there's never really a good reason to write a generic type this way, it's not considered to be problematic to do this.

Which inference algorithm is chosen is implementation-dependent and may change for necessary correctness or performance reasons; you should not take a dependency on one or the other occurring.

## Turn On This Flag To Do That

Many bug reports simply require certain flags to be turned on to get the desired behavior.

### Assume Array Access Might Be Out of Bounds: `noUncheckedIndexedAccess`

You can turn on `noUncheckedIndexedAccess` to change the behavior such that arrays and object maps presume possibly-out-of-bounds access.

This flag is a "big hammer", so to speak, and does not have any mechanisms for detecting provably in-bounds access. Because it's nearly impossible to soundly prove that an access is in-bounds (on account of mutation, etc.), such exceptions won't be considered.

### Require Properties to Either Be Missing or Not `undefined`: `exactOptionalPropertyTypes`

You can turn on `exactOptionalPropertyTypes` to change the behavior such that an optional property can't be explicitly provided with the value `undefined`.
This has important effects on things like object spread:
```ts
type Foo = { bar: number };
const base: Foo = { bar: 42 };
// Somewhat-suspect initialization of Partial<Foo>
const partial: Partial<Foo> = { bar: undefined };

// If using spread, the 'undefined' value can be present
// at Foo.bar
const possiblyBad: Foo = { ...base, ...partial };
```

This flag affects *all* optional properties and there is no mechanism for doing this on a per-type basis.

### Enforce `readonly` in Subtyping / Assignability

Enable `--enforceReadonly` (available in TypeScript 5.6; see [#58296](https://github.com/microsoft/TypeScript/pull/58296))

## Common Comments

### What Kind of Feedback Are You Looking For?

We greatly appreciate constructive and actionable feedback on all issues. Constructive and actionable means that we can read your comment and gain new information from it.

Good examples of constructive and actionable feedback:

 * Code examples demonstrating how you would have used or been helped by the feature
 * Examples of third-party libraries that already use patterns like this
 * Describing where existing workarounds fall short of the desired behavior

A good litmus test to see if feedback is useful is to consider the scenario where we have an idea for some feature *other* than exactly the one being proposed, and think it might solve the problem you're having when asking for a particular feature. If you just say "I need this feature", we can't look at your comment and evaluate if the other proposed feature would solve your problem.

Similarly, the same feature request might have many different interpretations. If you just say "I need this", we don't know which *this* you're talking about, and can't figure out which interpretation of the feature is the correct one to implement.

### Time Marches On

Comments noting how long an suggestion has been open, what year it is, etc., are not considered constructive or helpful. We ask that you not bother posting them.

Most programming languages in common use have been in development for decades at this point, and most common feature ideas can be noticed within the first couple years of use, so the only notable thing that we can deduce from a suggestion being longstanding is that the underlying language has withstood the test of time and is still in use *despite* lacking that feature - if anything, evidence against its priority.

Similarly, in successful languages, most ideas that are easy and good get implemented quickly, so if you're looking at a longstanding issue, it's like either much more difficult or much less good than you might be thinking it is. Engaging with the problem, or providing useful context on why you think the change would be helpful, are constructive ways that you can comment on these threads.

Extending long threads with unconstructive comments like this has many downsides:

 * Doesn't do anything to clarify why any particular change is more important than the thousands of other open suggestions (evolving a language is not a first-in first-out queue)
 * Makes it harder to find useful content in the thread itself
 * Adds noise to search results
 * Increases the number of times someone has to click "Show More" once we reach the pagination limit

### Can I Work On This?

If this is in the `Backlog` milestone, yes! See also https://github.com/microsoft/TypeScript/blob/main/CONTRIBUTING.md#issue-claiming. Issues may also be marked with "Help Wanted" but this label is not necessary; the milestone field is authoritative.

If an issue isn't in the `Backlog` milestone, please be advised that PRs may not be reviewed and may not be accepted.

### Any Updates?

There are generally no nonpublic updates that can be retrieved by asking "Any updates?".

Our [iteration plans](https://github.com/microsoft/TypeScript/labels/Planning), [meeting notes](https://github.com/microsoft/TypeScript/labels/Design%20Notes), and other plans are available for perusal.

If you'd like to see a particular bug fixed, feel free to raise it in the active milestone's iteration plan. We do our best to prioritize issues that people are encountering.

If there are features you'd like to see added:

 * If it's in the `Backlog` milestone, PRs are welcomed!
 * If it's not in the `Backlog` milestone, see "What Kind of Feedback Are You Looking For?"

### This Is Closed, But Should Be Open, Or Vice Versa

Every project on GitHub uses slightly different definitions of "Open" and "Closed".

For us, "Open" means "There is known work left to do". An unfixed bug, for example, should be left open. A suggestion we're still working out the details on, or assessing the need for, is also likely to be Open.

"Closed" means "There is not known work left to do". A fixed bug, for example, is always closed; there is nothing left to do. A suggestion which is clearly out-of-scope for the project, either by going against its core design principles, or being simply unrelated to its core goals, would also be closed.

Not every example is clear-cut. As with all things in life, when there are grey areas to be found, and it's [hard to find definitions that everyone agrees with](https://danluu.com/impossible-agree/).

These are the edge case rules that we've decided on for clarity:

 * A *bug* (i.e. known defect) that is so trivial as to not ever be bothersome (for example, a benign crash that only occurs in a file specifically crafted to overflow the parser stack) may be considered a "Won't Fix". In this case, it is *Closed* because there is not "work left to do"
 * A *suggestion* (feature idea) that seems quite unlikely to provide a good return on investment in the foreseeable future would be Closed, because we've done the work (gathered feedback, considered the pros and cons, and made a decision)
   * Sometimes the decision will be "Not right now, but definitely possibly later?" - in this case, Open is the correct state, because the work is "Keep collecting feedback to see how priorities change". A suggestion may be Open for a very long time as a result. We do not Close suggestions *simply* because they were brought up a long time ago, and don't Close issues simply because they haven't been done fast enough for someone's liking.
 * A *design limitation* (acknowledged problem, but one where we have no idea how to fix it, or how to fix it without unacceptable downsides) is Closed. These are difficult cases where we've agreed that the issue is a problem *per se*, but can't see any solution that would be acceptable. There is no "work to be done" here because "Make a breakthrough" is not work that can be concretely pursued.
   * If you think you have a solution to a problem marked "design limitation", feel free to send a PR, we will evaluate it.

All decisions are "only for now". The JS ecosystem is ever-changing, developer priorities are ever-changing, and the project itself will always be evolving over time. This doesn't mean we need to leave *every* issue Open just to leave open a slight chance of a future possibility -- the "Re-open" button is always there, and we're always capable of changing our minds in the face of new evidence or new circumstances. Open/Closed in these gray areas represents *our best guess* at the long-term state of all of these decisions.

It's worth noting that the Open/Close state flows *from* the maintainers' point of view *to* the issue, not vice versa. Reopening a Suggestion for a feature that we actively don't want to be part of the language won't make us start wanting it. Similarly, reopening a Design Limitation that we have no idea how to fix won't make us able to address it.

Open/Closed definition is a project-wide decision and we don't make per-issue deviations from this definition. Complaining about open/closed state isn't constructive, and please remember that insistence in engaging in nonconstructive discussion is against the [code of conduct](https://microsoft.github.io/codeofconduct/).

## Other FAQs and Errors

### Why Method Bivariance?

It seems like it should be really easy to add a `--strictMethodTypes` flag that works just like `--strictFunctionTypes` does today. What's the problem?

In short, even though this seems like it should be straightforward, there are a large number of common patterns today that depend on using method bivariance to cause types to subtype other types in ways that are idiomatic due to prior knowledge of ownership, conventions around who's allowed to raise event-like callbacks, and others. A cursory check in a small project shows hundreds of errors in longstanding code where there aren't any existing complaints of unsoundness due to bivariance. Without a way to fix these errors, there's not a tractable path forward to adding those errors in other places where bivariance definitely *is* a possible source of error.

Out of the gate, this breaks array covariance, and not even in a way that has an apparent fix. Let's reduce `Array<T>` and `ReadonlyArray<T>` to their smallest representations relevant to the problem at hand:
```ts
// The smallest possible read-only array that still has
// a useful method for getting a mutable copy, like you
// would expect to be able to get from Array#slice
interface MiniatureReadonlyArray<T> {
    getMutableCopy(): MiniatureMutableArray<T>;
    readonly [i: number]: T;
}

// Mutable array just adds one contravariant method
interface MiniatureMutableArray<T> extends MiniatureReadonlyArray<T> {
    push(arg: T): void;
}

// A read-only array of strings and numbers
declare const sn_mini: MiniatureReadonlyArray<string | number>;
// A should-be-legal covariant aliasing of that array
let snb_mini: MiniatureReadonlyArray<string | number | boolean> = sn_mini;
```
Under `strictMethodTypes`, this assignment actually fails. Why?

*It appears* that an illegal call is possible when you do this:
```ts
// Invalid: snb_mini is possibly an alias to sn_mini,
// whose getMutablyCopy's return type is MiniatureMutableArray<string | number>,
// whose `push` method cannot accept booleans
snb_mini.getMutableCopy().push(true);
```
This logic is sound given the definitions of types that we have.

However, we (as humans) know from reading the prose that when we call `getMutableCopy`, the *copy* we get is something we're free to mutate however we like.

Possible solutions to this problem are themselves quite difficult:
 * One option would be to have some kind of per-site annotation so that we could say that `getMutableCopy` doesn't return a `MiniatureMutableArray<T>`; instead it returns... well, something else. `MiniatureMutableArray< out T>` ? What are the semantics of this? When exactly is the covariant aliasing allowed? Can I get a reference to an `out string` if I start with a `MiniatureReadonlyArray<string>`? When does that modifier go away? It's not clear. If I knew what to write here I'd be proposing it.
 * Allow "forced" variance annotation, e.g. allow you to write something like `interface ReadonlyArray<out! T> {` that forces a covariant measurement of `T`. This isn't great either, because it means that structural and instantiation-based inferences and relational checks on `ReadonlyArray` would behave differently (see the FAQ entry on this). Since there's no guarantee which of those two checks you get, this just opens the door for a huge amount of fundamentally-unfixable inconsistencies whenever this type gets mentioned, which is going to be very common. *Worse*, since `interface Array<T>` and `interface ReadonlyArray<T>` *are* different interfaces, any time you bridge the mutability gap, you'll see the invariant behavior instead of the covariant behavior (since a variance annotation can't apply to a structural operation), so this problem would not actually go away *at all*.
 * Some kind of more-explicit "ownership" model like Rust's that gives more prescriptive rules around when something is allowed to be covariantly aliased and when it isn't. Again, I don't know what that looks like in TypeScript.

This also breaks function intersection with the built-in `Function`, again with no clear fix:
```ts
type SomeFunc = (s: string) => void;
declare const sf: SomeFunc;
// Illegal
const p: Function & SomeFunc = sf;
```
With the observed error - again, technically sound - that you can't call `p`'s `apply` method obtained from `Function`
```
error TS2322: Type 'SomeFunc' is not assignable to type 'Function & SomeFunc'.
  Type 'SomeFunc' is not assignable to type 'Function'.
    Types of property 'apply' are incompatible.
      Type '{ <T, R>(this: (this: T) => R, thisArg: T): R; <T, A extends any[], R>(this: (this: T, ...args: A) => R, thisArg: T, args: A): R; }' is not assignable to type '(this: Function, thisArg: any, argArray?: any) => any'.
```

Basic assignment to `readonly unknown[]` doesn't work, due to `concat`:
```
error TS2322: Type 'readonly string[]' is not assignable to type 'readonly unknown[]'.
  Types of property 'concat' are incompatible.
    Type '{ (...items: ConcatArray<string>[]): string[]; (...items: (string | ConcatArray<string>)[]): string[]; }' is not assignable to type '{ (...items: ConcatArray<unknown>[]): unknown[]; (...items: unknown[]): unknown[]; }'.
      Types of parameters 'items' and 'items' are incompatible.
        Type 'ConcatArray<unknown>' is not assignable to type 'ConcatArray<string>'.
          The types returned by 'slice(...).pop()' are incompatible between these types.
            Type 'unknown' is not assignable to type 'string | undefined'.

```

There's also a problem with the DOM, because the DOM types are constructed in a way that implicitly disallows some operations via a supertype alias, e.g. `addEventListener`

`@types/node` also produces hundreds of errors due to the eventing pattern, e.g.
```
node_modules/@types/node/child_process.d.ts:73:15 - error TS2430: Interface 'ChildProcess' incorrectly extends interface 'EventEmitter'.
  Types of property 'addListener' are incompatible.
    Type '{ (event: string, listener: (...args: any[]) => void): this; (event: "close", listener: (code: number | null, signal: Signals | null) => void): this; (event: "disconnect", listener: () => void): this; (event: "error", listener: (err: Error) => void): this; (event: "exit", listener: (code: number | null, signal: Sign...' is not assignable to type '(eventName: string | symbol, listener: (...args: any[]) => void) => this'.
      Types of parameters 'event' and 'eventName' are incompatible.
        Type 'string | symbol' is not assignable to type 'string'.
          Type 'symbol' is not assignable to type 'string'.

73     interface ChildProcess extends EventEmitter {
                 ~~~~~~~~~~~~
```

### The inferred type of "X" cannot be named without a reference to "Y". This is likely not portable. A type annotation is necessary

Let's say you use a package manager with strict dependencies:
```
|-/my_package_1
|-- /node_modules
|---- /other_package <- direct dependency
|------ index.d.ts
|------ /node_modules
|--------- /sub_dep <- symlink!
|----------- index.d.ts
|-- /src
|---- tsconfig.json  <- project root
|---- foo.ts         <- a source file that imports the above file
```
Where `foo.ts` looks like this:
```ts
import { getMakeSubDep } from "other_package";

// The inferred type of p refers to a type defined
// inside node_modules/other_package/node_modules/sub_dep/index.d.ts
export const p = getMakeSubDep();
```
When TypeScript needs to emit `foo.d.ts`, it needs to write out a type for `p`:
```ts
export const p: ???
```

What should go in the `???` ?

One option would be to use a relative path:
```ts
import { subdep } from "../node_modules/other_package/node_modules/sub_dep";
export const p: subdep
```
This is *obviously* wrong: It's implausible that a consumer of `foo.d.ts` would have a folder layout that matches what we happened to have here.

Another option would be to use a subpath:
```ts
import { subdep } from "other_package/node_modules/sub_dep";
export const p: subdep
```
This is *also* obviously wrong: The semantics of `other_package` are not that it exposes `node_modules/sub_dep` as as sub-path. This probably won't work at runtime, and even if it did, it's not what you want

Another option would be to use a module name:
```ts
import { subdep } from "sub_dep";
export const p: subdep
```

Is *this* correct?

If `other_package` has a dependency on `sub_dep@2.0.0` and *your* package has a dependency on `sub_dep@3.0.0`, then these aren't the same type, and it's wrong.

If you don't have a declared dependency on `sub_dep` at all, then this code would also fail to load `sub_dep` when ingested in a downstream project.

These situations - the non-working ones - are the "non-portable identifiers" that TypeScript is complaining about. TS was put into a position where it had to reference the name of a module, but it couldn't find a name for that module that appeared to work. This is why this error occurs.

Now, if you *do* have a declared dependency on `sub_dep`, and it resolves to the "same" target as the one `other_package` did, then this is fine. The way this works (roughly) is that TS keeps a reverse map of the files it has loaded and what module specifiers it used to find them. So if you have `import { subdep } from "sub_dep"`, and it resolved to a correct .d.ts file, then TS will have it in the lookup table and can use `sub_dep` to refer to the same file that `other_package` did even though these two module specifiers *may have* referred to different files (but they didn't).

But! If you never referred to `sub_dep` in your compilation, then TypeScript has never "seen" `sub_dep` be referred to from the module resolution context that `foo.d.ts` will have, so it doesn't know whether or not `"sub_dep"` is a legal way to refer to `other_package/node_modules/sub_dep`, and ends up issuing this error.

The correct way to address this is generally to import the type that the `.d.ts` needs to refer to:
```ts
// in foo.ts
import { subdep } from "sub_dep";
import { getMakeSubDep } from "other_package";

// No additional type annotation needed
export const p = getMakeSubDep();
```
If *you* can't do this, then TypeScript can't either, and the only real alternative is to use a broader type annotation so that TS doesn't need to refer to types which are impossible to name:
```ts
// in foo.ts
import { getMakeSubDep } from "other_package";

// Annotate to anonymous version of whatever subdep is
export const p: { isSubDep: boolean } = getMakeSubDep();
```
Or restructure the input file in such a way that the referenced type is not exposed in the `.d.ts`.

As of TypeScript 5.5 (see #58176), this error should never occur if the required dependency is in your project's `package.json`.

--------------

# FAQ Archive

<!-- Unmigrated content down here -->

## Common "Bugs" That Aren't Bugs

> I've found a long-overlooked bug in TypeScript!

Here are some behaviors that may look like bugs, but aren't.

* These two empty classes can be used in place of each other
  * See the [FAQ Entry on this page](#why-do-these-empty-classes-behave-strangely)
* I can use a non-`void`-returning function where one returning `void` is expected
  * See the [FAQ Entry on this page](#why-are-functions-returning-non-void-assignable-to-function-returning-void)
  * Prior discussion at #4544
* I'm allowed to use a shorter parameter list where a longer one is expected
  * See the [FAQ Entry on this page](#why-are-functions-with-fewer-parameters-assignable-to-functions-that-take-more-parameters)
  * Prior discussion at #370, #9300, #9765, #9825, #13043, #16871, #13529, #13977, #17868, #20274, #20541, #21868, #26324, #30876
* `private` class members are actually visible at runtime
  * See the [FAQ Entry on this page](#you-should-emit-classes-like-this-so-they-have-real-private-members) for a commonly suggested "fix"
  * Prior discussion at #564, #1537, #2967, #3151, #6748, #8847, #9733, #11033
* This conditional type returns `never` when it should return the true branch.
  * See this [issue](https://github.com/microsoft/TypeScript/issues/31751) for discussion about _distributive conditional types_.
* This mapped type returns a primitive type, not an object type.
  * Mapped types declared as `{ [ K in keyof T ]: U }` where T is a type parameter are known as _homomorphic mapped types_, which means that the mapped type is a structure preserving function of `T`. When type parameter `T` is instantiated with a primitive type the mapped type evaluates to the same primitive.
* A method and a function property of the same type behave differently.
  * Methods are always bivariant in their argument, while function properties are contravariant in their argument under `strictFunctionTypes`. More discussion [here](https://github.com/microsoft/TypeScript/pull/18654).
* Export maps aren't respected.
  * TypeScript's support for export maps is recent, and requires `moduleResolution` be set to `node16` or `nodenext` to be respected.
* A default import of a commonjs module with a default in a esm file doesn't seem to be the default export of that module when `module` is `node16` or `nodenext`.
  * TypeScript is exposing `node`'s behavior here - when a esm module default imports a commonjs module, that whole commonjs module is made available as the default import. If you then want the actual default member of that module, you'll need to access the `default` member of that import. Refer to the [node documentation](https://nodejs.org/api/esm.html#commonjs-namespaces) for more information.

## Common Feature Requests
> I want to request one of the following features...

Here's a list of common feature requests and their corresponding issue.
Please leave comments in these rather than logging new issues.
* Safe navigation operator, AKA CoffeeScript's null conditional/propagating/propagation operator, AKA C#'s' `?.` operator [#16](https://github.com/Microsoft/TypeScript/issues/16)
* Minification [#8](https://github.com/Microsoft/TypeScript/issues/8)
* Extension methods [#9](https://github.com/Microsoft/TypeScript/issues/9)
* Partial classes [#563](https://github.com/Microsoft/TypeScript/issues/563)
* Something to do with `this` [#513](https://github.com/Microsoft/TypeScript/issues/513)
* Strong typing of `Function` members `call`/`bind`/`apply` [#212](https://github.com/Microsoft/TypeScript/issues/212)
* Runtime function overloading [#3442](https://github.com/Microsoft/TypeScript/issues/3442)

## Type System Behavior

### What is structural typing?
TypeScript uses *structural typing*.
This system is different than the type system employed by some other popular languages you may have used (e.g. Java, C#, etc.)

The idea behind structural typing is that two types are compatible if their *members* are compatible.
For example, in C# or Java, two classes named `MyPoint` and `YourPoint`, both with public `int` properties `x` and `y`, are not interchangeable, even though they are identical.
But in a structural type system, the fact that these types have different names is immaterial.
Because they have the same members with the same types, they are identical.

This applies to subtype relationships as well.
In C++, for example, you could only use a `Dog` in place of an `Animal` if `Animal` was explicitly in `Dog`'s class heritage.
In TypeScript, this is not the case - a `Dog` with at least as many members (with appropriate types) as `Animal` is a subtype of `Animal` regardless of explicit heritage.

This can have some surprising consequences for programmers accustomed to working in a nominally-typed language.
Many questions in this FAQ trace their roots to structural typing and its implications.
Once you grasp the basics of it, however, it's very easy to reason about.

### What is type erasure?

TypeScript *removes* type annotations, interfaces, type aliases, and other type system constructs during compilation.

Input:

```ts
var x: SomeInterface;
```

Output:

```js
var x;
```

This means that at run-time, there is no information present that says that some variable `x` was declared as being of type `SomeInterface`.

The lack of run-time type information can be surprising for programmers who are used to extensively using reflection or other metadata systems.
Many questions in this FAQ boil down to "because types are erased".


### Why are function parameters bivariant?

 > I wrote some code like this and expected an error:
 > ```ts
 > function trainDog(d: Dog) { ... }
 > function cloneAnimal(source: Animal, done: (result: Animal) => void): void { ... }
 > let c = new Cat();
 >
 > // Runtime error here occurs because we end up invoking 'trainDog' with a 'Cat'
 > cloneAnimal(c, trainDog);
 > ```

This is an unsoundness resulting from the lack of explicit covariant/contravariant annotations in the type system.
Because of this omission, TypeScript must be more permissive when asked whether `(x: Dog) => void` is assignable to `(x: Animal) => void`.

To understand why, consider two questions: Is `Dog[]` a subtype of `Animal[]`? *Should* `Dog[]` be a subtype of `Animal[]` in TypeScript?

The second question (*should* `Dog[]` be a subtype of `Animal[]`?) is easier to analyze.
What if the answer was "no"?

```ts
function checkIfAnimalsAreAwake(arr: Animal[]) { ... }

let myPets: Dog[] = [spot, fido];

// Error? Can't substitute Dog[] for Animal[]?
checkIfAnimalsAreAwake(myPets);
```

This would be *incredibly annoying*.
The code here is 100% correct provided that `checkIfAnimalsAreAwake` doesn't modify the array.
There's not a good reason to reject this program on the basis that `Dog[]` can't be used in place of `Animal[]` - clearly a group of `Dog`s is a group of `Animal`s here.

Back to the first question.
When the type system decides whether or not `Dog[]` is a subtype of `Animal[]`, it does the following computation (written here as if the compiler took no optimizations), among many others:

 * Is `Dog[]` assignable to `Animal[]`?
  * Is each member of `Dog[]` assignable to `Animal[]`?
    * Is `Dog[].push` assignable to `Animal[].push`?
      * Is the type `(x: Dog) => number` assignable to `(x: Animal) => number`?
        * Is the first parameter type in `(x: Dog) => number` assignable to or from first parameter type in `(x: Animal) => number`?
          * Is `Dog` assignable to or from `Animal`?
            * Yes.

As you can see here, the type system must ask "Is the type `(x: Dog) => number` assignable to `(x: Animal) => number`?",
which is the same question the type system needed to ask for the original question.
If TypeScript forced contravariance on parameters (requiring `Animal` being assignable to `Dog`), then `Dog[]` would not be assignable to `Animal[]`.

In summary, in the TypeScript type system, the question of whether a more-specific-type-accepting function should be assignable to a function accepting a less-specific type provides a prerequisite answer to whether an *array* of that more specific type should be assignable to an array of a less specific type.
Having the latter *not* be the case would not be an acceptable type system in the vast majority of cases,
so we have to take a correctness trade-off for the specific case of function argument types.

### Why are functions with fewer parameters assignable to functions that take more parameters?


### Why are functions returning non-`void` assignable to function returning `void`?

> I wrote some code like this and expected an error:
> ```ts
> function doSomething(): number {
>     return 42;
> }
>
> function callMeMaybe(callback: () => void) {
>     callback();
> }
>
> // Expected an error because 'doSomething' returns number, but 'callMeMaybe'
> // expects void-returning function
> callMeMaybe(doSomething);
> ```

This is the expected and desired behavior.
First, refer to the "substitutability" primer -- the fact that `doSomething` returns "more" information than `callMeMaybe` is a valid substitution.

Second, let's consider another case:
```ts
let items = [1, 2];
callMeMaybe(() => items.push(3));
```
This is isomorphic to the example that "wanted" an error.
`Array#push` returns a number (the new length of the array), but it's a safe substitute to use for a `void`-returning function.

Another way to think of this is that a `void`-returning callback type says "I'm not going to look at your return value, if one exists".

### Why are all types assignable to empty interfaces?

> I wrote some code like this and expected an error:
> ```ts
> interface Thing { /* nothing here */ }
> function doSomething(a: Thing) {
>   // mysterious implementation here
> }
> // Expected some or all of these to be errors
> doSomething(window);
> doSomething(42);
> doSomething('huh?');
> ```

Types with no members can be substituted by *any* type.
In this example, `window`, `42`, and `'huh?'` all have the required members of a `Thing` (there are none).

In general, you should *never* find yourself declaring an `interface` with no properties.

### Can I make a type alias nominal?

> I wrote the following code and expected an error:
> ```ts
> type SomeUrl = string;
> type FirstName = string;
> let x: SomeUrl = "http://www.typescriptlang.org/";
> let y: FirstName = "Bob";
> x = y; // Expected error
> ```

Type aliases are simply *aliases* -- they are indistinguishable from the types they refer to.

A workaround involving intersection types to make "branded primitives" is possible:
```ts
// Strings here are arbitrary, but must be distinct
type SomeUrl = string & {'this is a url': {}};
type FirstName = string & {'person name': {}};

// Add type assertions
let x = <SomeUrl>'';
let y = <FirstName>'bob';
x = y; // Error

// OK
let xs: string = x;
let ys: string = y;
xs = ys;
```
You'll need to add a type assertion wherever a value of this type is created.
These can still be aliased by `string` and lose type safety.

### How do I prevent two types from being structurally compatible?
> I would like the following code to produce an error:
> ```ts
> interface ScreenCoordinate {
>   x: number;
>   y: number;
> }
> interface PrintCoordinate {
>   x: number;
>   y: number;
> }
> function sendToPrinter(pt: PrintCoordinate) {
>   // ...
> }
> function getCursorPos(): ScreenCoordinate {
>   // Not a real implementation
>   return { x: 0, y: 0 };
> }
> // This should be an error
> sendToPrinter(getCursorPos());
> ```

A possible fix if you really want two types to be incompatible is to add a 'brand' member:
```ts
interface ScreenCoordinate {
  _screenCoordBrand: any;
  x: number;
  y: number;
}
interface PrintCoordinate {
  _printCoordBrand: any;
  x: number;
  y: number;
}

// Error
sendToPrinter(getCursorPos());
```

Note that this will require a type assertion wherever 'branded' objects are created:
```ts
function getCursorPos(): ScreenCoordinate {
  // Not a real implementation
  return <ScreenCoordinate>{ x: 0, y: 0 };
}
```

See also [#202](https://github.com/Microsoft/TypeScript/issues/202) for a suggestion tracking making this more intuitive.

### How do I check at run-time if an object implements some interface?

> I want to write some code like this:
> ```ts
> interface SomeInterface {
>   name: string;
>   length: number;
> }
> interface SomeOtherInterface {
>   questions: string[];
> }
>
> function f(x: SomeInterface|SomeOtherInterface) {
>   // Can't use instanceof on interface, help?
>   if (x instanceof SomeInterface) {
>     // ...
>   }
> }
> ```

TypeScript types are erased (https://en.wikipedia.org/wiki/Type_erasure) during compilation.
This means there is no built-in mechanism for performing runtime type checks.
It's up to you to decide how you want to distinguish objects.
A popular method is to check for properties on an object.
You can use user-defined type guards to accomplish this:

```ts
function isSomeInterface(x: any): x is SomeInterface {
  return typeof x.name === 'string' && typeof x.length === 'number';

function f(x: SomeInterface|SomeOtherInterface) {
  if (isSomeInterface(x)) {
    console.log(x.name); // Cool!
  }
}
```

### Why doesn't this incorrect cast throw a runtime error?
> I wrote some code like this:
> ```ts
> let x: any = true;
> let y = <string>x; // Expected: runtime error (can't convert boolean to string)
> ```
or this
> ```ts
> let a: any = 'hmm';
> let b = a as HTMLElement; // expected b === null
> ```

TypeScript has *type assertions*, not *type casts*.
The intent of `<T>x` is to say "TypeScript, please treat `x` as a `T`", not to perform a type-safe run-time conversion.
Because types are erased, there is no direct equivalent of C#'s `expr as` type or `(type)expr` syntax.


### Why don't I get type checking for `(number) => string` or `(T) => T`?
> I wrote some code like this and expected an error:
> ```ts
> let myFunc: (number) => string = (n) => 'The number in hex is ' + n.toString(16);
> // Expected error because boolean is not number
> console.log(myFunc(true));
> ```

Parameter names in function types are required.
The code as written describes a function taking one parameter named `number` of type `any`.
In other words, this declaration
```ts
let myFunc: (number) => string;
```
is equivalent to this one
```ts
let myFunc: (number: any) => string;
```

You should instead write:
```ts
let myFunc: (myArgName: number) => string;
```

To avoid this problem, turn on the `noImplicitAny` flag, which will issue a warning about the implicit `any` parameter type.

### Why am I getting an error about a missing index signature?

> These three functions seem to do the same thing, but the last one is an error. Why is this the case?
> ```ts
> interface StringMap {
>   [key: string]: string;
> }
>
> function a(): StringMap {
>   return { a: "1" }; // OK
> }
>
> function b(): StringMap {
>   var result: StringMap = { a: "1" };
>   return result; // OK
> }
>
> function c(): StringMap {
>   var result = { a: "1" };
>   return result; // Error - result lacks index signature, why?
> }
> ```

This isn't now an error in TypeScript 1.8 and later. As for earlier versions:

Contextual typing occurs when the context of an expression gives a hint about what its type might be. For example, in this initialization:

```ts
var x: number = y;
```

The expression `y` gets a contextual type of `number` because it's initializing a value of that type. In this case, nothing special happens, but in other cases more interesting things will occur.

One of the most useful cases is functions:

```ts
// Error: string does not contain a function called 'ToUpper'
var x: (n: string) => void = (s) => console.log(s.ToUpper());
```

How did the compiler know that `s` was a `string`? If you wrote that function expression by itself, `s` would be of type `any` and there wouldn't be any error issued. But because the function was contextually typed by the type of `x`, the parameter `s` acquired the type `string`. Very useful!

At the same time, an index signature specifies the type when an object is indexed by a `string` or a `number`. Naturally, these signatures are part of type checking:

```ts
var x: { [n: string]: Car; };
var y: { [n: string]: Animal; };
x = y; // Error: Cars are not Animals, this is invalid
```

The lack of an index signature is also important:

```ts
var x: { [n: string]: Car; };
var y: { name: Car; };
x = y; // Error: y doesn't have an index signature that returns a Car
```

The problem with assuming that objects don't have index signatures is that you then have no way to initialize an object with an index signature:

```ts
var c: Car;
// Error, or not?
var x: { [n: string]: Car } = { 'mine': c };
```

The solution is that when an object literal is contextually typed by a type with an index signature, that index signature is added to the type of the object literal if it matches. For example:

```ts
var c: Car;
var a: Animal;
// OK
var x: { [n: string]: Car } = { 'mine': c };
// Not OK: Animal is not Car
var y: { [n: string]: Car } = { 'mine': a };
```

Let's look at the original function:

```ts
function c(): StringMap {
  var result = { a: "1" };
  return result; // Error - result lacks index signature, why?
}
```

Because `result`'s type does not have an index signature, the compiler throws an error.

### Why am I getting `Supplied parameters do not match any signature` error?

A function or a method implementation signature is not part of the overloads.

```ts
function createLog(message:string): number;
function createLog(source:string, message?:string): number {
  return 0;
}

createLog("message"); // OK
createLog("source", "message"); // ERROR: Supplied parameters do not match any signature
```

When having at least one overload signature declaration, only the overloads are visible. The last signature declaration, also known as the implementation signature, does not contribute to the shape of your signature. So to get the desired behavior you will need to add an additional overload:

```ts
function createLog(message:string): number;
function createLog(source:string, message:string): number
function createLog(source:string, message?:string): number {
  return 0;
}
```

The rationale here is that since JavaScript does not have function overloading, you will be doing parameter checking in your function, and this your function implementation might be more permissive than what you would want your users to call you through.

For instance you can require your users to call you using matching pairs of arguments, and implement this correctly without having to allow mixed argument types:

```ts
function compare(a: string, b: string): void;
function compare(a: number, b: number): void;
function compare(a: string|number, b: string|number): void {
  // Just an implementation and not visible to callers
}

compare(1,2) // OK
compare("s", "l") // OK
compare (1, "l") // Error.
```

-------------------------------------------------------------------------------------

## Classes

### Why do these empty classes behave strangely?

> I wrote some code like this and expected an error:
> ```ts
> class Empty { /* empty */ }
>
> var e2: Empty = window;
> ```

See the question ["Why are all types assignable to empty interfaces?"](#why-are-all-types-assignable-to-empty-interfaces) in this FAQ.
It's worth re-iterating the advice from that answer: in general, you should *never* declare a `class` with no properties.
This is true even for subclasses:

```ts
class Base {
  important: number;
  properties: number;
}
class Alpha extends Base { }
class Bravo extends Base { }
```

`Alpha` and `Bravo` are structurally identical to each other, and to `Base`.
This has a lot of surprising effects, so don't do it!
If you want `Alpha` and `Bravo` to be different, add a private property to each.

### When and why are classes nominal?

What explains the difference between these two lines of code?
```ts
class Alpha { x: number }
class Bravo { x: number }
class Charlie { private x: number }
class Delta { private x: number }

let a = new Alpha(), b = new Bravo(), c = new Charlie(), d = new Delta();

a = b; // OK
c = d; // Error
```

In TypeScript, classes are compared structurally.
The one exception to this is `private` and `protected` members.
When a member is private or protected, it must *originate in the same declaration* to be considered the same as another private or protected member.


### Why does `this` get orphaned in my instance methods?

> I wrote some code like this:
> ```ts
> class MyClass {
>   x = 10;
>   someCallback() {
>     console.log(this.x); // Prints 'undefined', not 10
>     this.someMethod(); // Throws error "this.method is not a function"
>   }
>   someMethod() {
>
>   }
> }
>
> let obj = new MyClass();
> window.setTimeout(obj.someCallback, 10);
> ```

Synonyms and alternate symptoms:
> * Why are my class properties `undefined` in my callback?
> * Why does `this` point to `window` in my callback?
> * Why does `this` point to `undefined` in my callback?
> * Why am I getting an error `this.someMethod is not a function`?
> * Why am I getting an error `Cannot read property 'someMethod' of undefined`?

In JavaScript, the value of `this` inside a function is determined as follows:
 1. Was the function the result of calling `.bind`? If so, `this` is the first argument passed to `bind`
 2. Was the function *directly* invoked via a property access expression `expr.method()`? If so, `this` is `expr`
 3. Otherwise, `this` is `undefined` (in "strict" mode), or `window` in non-strict mode

The offending problem is this line of code:
```ts
window.setTimeout(obj.someCallback, 10);
```
Here, we provided a function reference to `obj.someCallback` to `setTimeout`.
The function was then invoked on something that wasn't the result of `bind` and wasn't *directly* invoked as a method.
Thus, `this` in the body of `someCallback` referred to `window` (or `undefined` in strict mode).

Solutions to this are outlined here: http://stackoverflow.com/a/20627988/1704166

### What's the difference between `Bar` and `typeof Bar` when `Bar` is a `class`?
> I wrote some code like this and don't understand the error I'm getting:
> ```ts
> class MyClass {
>   someMethod() { }
> }
> var x: MyClass;
> // Cannot assign 'typeof MyClass' to MyClass? Huh?
> x = MyClass;
> ```

It's important to remember that in JavaScript, classes are just functions.
We refer to the class object itself -- the *value* `MyClass` -- as a *constructor function*.
When a constructor function is invoked with `new`, we get back an object that is an *instance* of the class.

So when we define a class, we actually define two different *types*.

The first is the one referred to by the class' name; in this case, `MyClass`.
This is the *instance* type of the class.
It defines the properties and methods that an *instance* of the class has.
It's the type returned by invoking the class' constructor.

The second type is anonymous.
It is the type that the constructor function has.
It contains a *construct signature* (the ability to be invoked with `new`) that returns an *instance* of the class.
It also contains any `static` properties and methods the class might have.
This type is typically referred to as the "static side" of the class because it contains those static members (as well as being the *constructor* for the class).
We can refer to this type with the type query operator `typeof`.

The `typeof` operator (when used in a *type* position) expresses the *type* of an *expression*.
Thus, `typeof MyClass` refers to the type of the expression `MyClass` - the *constructor function* that produces instances of `MyClass`.


### Why do my derived class property initializers overwrite values set in the base class constructor?
See [#1617](https://github.com/Microsoft/TypeScript/issues/1617) for this and other initialization order questions


### What's the difference between `declare class` and `interface`?

TODO: Write up common symptoms of `declare class` / `interface` confusion.

See http://stackoverflow.com/a/14348084/1704166


### What does it mean for an interface to extend a class?

> What does this code mean?
>
> ```ts
> class Foo {
>   /* ... */
> }
> interface Bar extends Foo {
>
> }
> ```

This makes a type called `Bar` that has the same members as the instance shape of `Foo`.
However, if `Foo` has private members, their corresponding properties in `Bar` must be implemented
by a class which has `Foo` in its heritage.
In general, this pattern is best avoided, especially if `Foo` has private members.

### Why am I getting "TypeError: [base class name] is not defined in `__extends`?
> I wrote some code like this:
> ```ts
> /** file1.ts **/
> class Alpha { /* ... */ }
>
> /** file2.ts **/
> class Bravo extends Alpha { /* ... */ }
> ```
> I'm seeing a runtime error in `__extends`:
> ```
> Uncaught TypeError: Alpha is not defined
> ```

The most common cause of this is that your HTML page includes a `<script>` tag for file2.ts, but not file1.ts.
Add a script tag for the base class' output *before* the script tag for the derived class.

### Why am I getting "TypeError: Cannot read property 'prototype' of undefined" in `__extends`?
> I wrote some code:
> ```ts
> /** file1.ts **/
> class Alpha { /* ... */ }
>
> /** file2.ts **/
> class Bravo extends Alpha { /* ... */ }
> ```
> I'm seeing a runtime error in `__extends`:
> ```
> Uncaught TypeError: Cannot read property 'prototype' of undefined
> ```

This can happen for a few reasons.

The first is that, within a single file, you defined the derived class *before* the base class.
Re-order the file so that base classes are declared before the derived classes.

If you're using `--out`, the compiler may be confused about what order you intended the files to be in.
See the section "How do I control file ordering..." in the FAQ.

If you're not using `--out`, your script tags in the HTML file may be the wrong order.
Re-order your script tags so that files defining base classes are included before the files defining the derived classes.

Finally, if you're using a third-party bundler of some sort, that bundler may be ordering files incorrectly.
Refer to that tool's documentation to understand how to properly order the input files in the resulting output.


### Why doesn't extending built-ins like `Error`, `Array`, and `Map` work?

In ES2015, constructors which return an object implicitly substitute the value of `this` for any callers of `super(...)`.
It is necessary for generated constructor code to capture any potential return value of `super(...)` and replace it with `this`.

As a result, subclassing `Error`, `Array`, and others may no longer work as expected.
This is due to the fact that constructor functions for `Error`, `Array`, and the like use ECMAScript 6's `new.target` to adjust the prototype chain;
however, there is no way to ensure a value for `new.target` when invoking a constructor in ECMAScript 5.
Other downlevel compilers generally have the same limitation by default.

**Example**

For a subclass like the following:

```ts
class FooError extends Error {
    constructor(m: string) {
        super(m);
    }
    sayHello() {
        return "hello " + this.message;
    }
}
```

you may find that:

* methods may be `undefined` on objects returned by constructing these subclasses, so calling `sayHello` will result in an error.
* `instanceof` will be broken between instances of the subclass and their instances, so `(new FooError()) instanceof FooError` will return `false`.

**Recommendation**

As a recommendation, you can manually adjust the prototype immediately after any `super(...)` calls.

```ts
class FooError extends Error {
    constructor(m: string) {
        super(m);

        // Set the prototype explicitly.
        Object.setPrototypeOf(this, FooError.prototype);
    }

    sayHello() {
        return "hello " + this.message;
    }
}
```

However, any subclass of `FooError` will have to manually set the prototype as well.
For runtimes that don't support [`Object.setPrototypeOf`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/setPrototypeOf), you may instead be able to use [`__proto__`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/proto).

Unfortunately, [these workarounds will not work on Internet Explorer 10 and prior](https://msdn.microsoft.com/en-us/library/s4esdbwz(v=vs.94).aspx).
One can manually copy methods from the prototype onto the instance itself (i.e. `FooError.prototype` onto `this`), but the prototype chain itself cannot be fixed.

-------------------------------------------------------------------------------------

## Generics

### Why is `A<string>` assignable to `A<number>` for `interface A<T> { }`?

> I wrote some code and expected an error:
> ```ts
> interface Something<T> {
>   name: string;
> }
> let x: Something<number>;
> let y: Something<string>;
> // Expected error: Can't convert Something<number> to Something<string>!
> x = y;
> ```

TypeScript uses a structural type system.
When determining compatibility between `Something<number>` and `Something<string>`, we examine each *member* of each type.
If all of the members are compatible, then the types themselves are compatible.
But because `Something<T>` doesn't *use* `T` in any member, it doesn't matter what type `T` is - it has no bearing on whether the types are compatible.

In general, you should *never* have type parameters which are unused.
The type will have unexpected compatibility (as shown here) and will also fail to have proper generic type inference in function calls.

### Why doesn't type inference work on this interface: `interface Foo<T> { }`?

> I wrote some code like this:
>
> ```ts
> interface Named<T> {
>   name: string;
> }
> class MyNamed<T> implements Named<T> {
>   name: 'mine';
> }
> function findByName<T>(x: Named<T>): T {
>   // TODO: Implement
>   return undefined;
> }
>
> var x: MyNamed<string>;
> var y = findByName(x); // expected y: string, got y: {}
> ```

TypeScript uses a structural type system.
This structural-ness also applies during generic type inference.
When inferring the type of `T` in the function call, we try to find *members* of type `T` on the `x` argument to figure out what `T` should be.
Because there are no members which use `T`, there is nothing to infer from, so we return `{}`.

Note that if you use `T`, you get correct inference:
```ts
interface Named<T> {
  name: string;
  value: T; // <-- added
}
class MyNamed<T> implements Named<T> {
  name: 'mine';
  value: T; // <-- added
}
function findByName<T>(x: Named<T>): T {
  // TODO: Implement
  return undefined;
}

var x: MyNamed<string>;
var y = findByName(x); // got y: string;
```

Remember: You should *never* have unused type parameters!
See the previous question for more reasons why this is bad.

### Why can't I write `typeof T`, `new T`, or `instanceof T` in my generic function?

> I want to write some code like this:
> ```ts
> function doSomething<T>(x: T) {
>   // Can't find name T?
>   let xType = typeof T;
>   let y = new xType();
>   // Same here?
>   if(someVar instanceof typeof T) {
>
>   }
>   // How do I instantiate?
>   let z = new T();
> }
> ```

Generics are erased during compilation.
This means that there is no *value* `T` at runtime inside `doSomething`.
The normal pattern that people try to express here is to use the constructor function for a class either as a factory or as a runtime type check.
In both cases, using a *construct signature* and providing it as a parameter will do the right thing:

```ts
function create<T>(ctor: { new(): T }) {
  return new ctor();
}
var c = create(MyClass); // c: MyClass

function isReallyInstanceOf<T>(ctor: { new(...args: any[]): T }, obj: T) {
  return obj instanceof ctor;
}
```

-------------------------------------------------------------------------------------

## Commandline Behavior

### Why did adding an `import` or `export` modifier break my program?

> I wrote a program:
> ```ts
> /* myApp.ts */
> function doSomething() {
>     console.log('Hello, world!');
> }
> doSomething();
> ```
>
> I compiled it with `tsc --module commonjs myApp.ts --out app.js` and ran `node app.js` and got the expected output.
>
> Then I added an `import` to it:
> ```ts
> import fs = require('fs');
> function doSomething() {
>     console.log('Hello, world!');
> }
> doSomething();
> ```
>
> Or added an `export` to it:
> ```ts
> export function doSomething() {
>     console.log('Hello, world!');
> }
> doSomething();
> ```
>
> And now nothing happens when I run `app.js`!

Modules -- those files containing top-level `export` or `import` statements -- are always compiled 1:1 with their corresponding js files.
The `--out` option only controls where *script* (non-module) code is emitted.
In this case, you should be running `node myApp.js`, because the *module* `myApp.ts` is always emitted to the file `myApp.js`.

This behavior has been fixed as of TypeScript 1.8; combining `--out` and `--module` is now an error for CommonJS module output.

### What does the error "Exported variable [name] has or is using private name [name]" mean?

This error occurs when you use the `--declaration` flag because the compiler is trying to produce a declaration file that *exactly* matches the module you defined.

Let's say you have this code:
```ts
/// MyFile.ts
class Test {
    // ... other members ....
    constructor(public parent: Test){}
}

export let t = new Test("some thing");
```

To produce a declaration file, the compiler has to write out a type for `t`:
```ts
/// MyFile.d.ts, auto-generated
export let t: ___fill in the blank___;
```

The member `t` has the type `Test`. The type `Test` is not visible because it's not exported, so we can't write `t: Test`.

In the *very simplest* cases, we could rewrite `Test`'s shape as an object type literal. But for the vast majority of cases, this doesn't work. As written, `Test`'s shape is self-referential and can't be written as an anonymous type. This also doesn't work if `Test` has any private or protected members. So rather than let you get 65% of the way through writing a realistic class and then start erroring then, we just issue the error (you're almost certainly going to hit later anyway) right away and save you the trouble.

To avoid this error:

1. Export the declarations used in the type in question
2. Specify an explicit type annotation for the compiler to use when writing declarations.

## `tsconfig.json` Behavior

### How can I specify an `include`?

There is no way now to indicate an `"include"` to a file outside the current folder in the `tsconfig.json` (tracked by [#1927](https://github.com/Microsoft/TypeScript/issues/1927)). You can achieve the same result by either:

1. Using a `"files"` list, or ;
2. Adding a `/// <reference path="..." />` directive in one of the files in your directory.

### Why am I getting the `error TS5055: Cannot write file 'xxx.js' because it would overwrite input file.` when using JavaScript files?

For a TypeScript file, the TypeScript compiler by default emits the generated JavaScript files in the same directory with the same base file name.
Because the TypeScript files and emitted JavaScript files always have different file extensions, it is safe to do so.
However, if you have set the `allowJs` compiler option to `true` and didn't set any emit output options (`outFile` and `outDir`), the compiler will try to emit JavaScript source files by the same rule, which will result in the emitted JavaScript file having the same file name with the source file. To avoid accidentally overwriting your source file, the  compiler will issue this warning and skip writing the output files.

There are multiple ways to solve this issue, though all of them involve configuring compiler options, therefore it is recommended that you have a `tsconfig.json` file in the project root to enable this.
If you don't want JavaScript files included in your project at all, simply set the `allowJs` option to `false`;
If you do want to include and compile these JavaScript files, set the `outDir` option or `outFile` option to direct the emitted files elsewhere, so they won't conflict with your source files;
If you just want to include the JavaScript files for editing and don't need to compile, set the `noEmit` compiler option to `true` to skip the emitting check.

-------------------------------------------------------------------------------------

# Glossary and Terms in this FAQ

### Dogs, Cats, and Animals, Oh My
For some code examples, we'll use a hypothetical type hierarchy:
```ts
       Animal
      /      \
    Dog      Cat
```
That is, all `Dog`s are `Animal`s, all `Cat`s are `Animal`s, but e.g. a function expecting a `Dog` cannot accept an argument of type `Cat`.
If you want to try these examples in the TypeScript Playground, start with this template:
```ts
interface Animal {
  move(): void;
}
interface Dog extends Animal {
  woof: string;
}
interface Cat extends Animal {
  meow: string;
}
```

Other examples will use DOM types like `HTMLElement` and `HTMLDivElement` to highlight concrete real-world implications of certain behaviors.

### "Substitutability"
Many answers relating to the type system make reference to [Substitutability](https://en.wikipedia.org/wiki/Liskov_substitution_principle).
This is a principle that says that if an object `X` can be used in place of some object `Y`, then `X` is a *subtype* of `Y`.
We also commonly say that `X` is *assignable to* `Y` (these terms have slightly different meanings in TypeScript, but the difference is not important here).

In other words, if I ask for a `fork`, a `spork` is an acceptable *substitute* because it has the same functions and properties of a `fork` (three prongs and a handle).

### Trailing, leading, and detached comments
TypeScript classifies comments into three different types:

- Leading comment  : a comment before a node followed by newline.
- Trailing comment : a comment after a node and in the same line as the node.
- Detached comment : a comment that is not part of any node such as copyright comment.
```ts
/*! Top-of-file copyright comment is a detached comment */

/* Leading comments of the function AST node */
function foo /* trailing comments of the function name, "foo", AST node */ () {
  /* Detached comment */

  let x = 10;
}
```

----------------------------------------------------------------------------------------

## Dead Links Parking Lot

### Why is a file in the `exclude` list still picked up by the compiler?

