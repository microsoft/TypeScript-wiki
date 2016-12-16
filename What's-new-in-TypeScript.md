# TypeScript 2.1

## `keyof` and Lookup Types

In JavaScript it is fairly common to have APIs that expect property names as parameters, but so far it hasn't been possible to express the type relationships that occur in those APIs.

Enter Index Type Query or `keyof`;
An indexed type query `keyof T` yields the type of permitted property names for `T`.
A `keyof T` type is considered a subtype of `string`.

##### Example

```ts
interface Person {
    name: string;
    age: number;
    location: string;
}

type K1 = keyof Person; // "name" | "age" | "location"
type K2 = keyof Person[];  // "length" | "push" | "pop" | "concat" | ...
type K3 = keyof { [x: string]: Person };  // string
```

The dual of this is *indexed access types*, also called *lookup types*.
Syntactically, they look exactly like an element access, but are written as types:

##### Example

```ts
type P1 = Person["name"];  // string
type P2 = Person["name" | "age"];  // string | number
type P3 = string["charAt"];  // (pos: number) => string
type P4 = string[]["push"];  // (...items: string[]) => number
type P5 = string[][0];  // string
```

You can use this pattern with other parts of the type system to get type-safe lookups.

```ts
function getProperty<T, K extends keyof T>(obj: T, key: K) {
    return obj[key];  // Inferred type is T[K]
}

function setProperty<T, K extends keyof T>(obj: T, key: K, value: T[K]) {
    obj[key] = value;
}

let x = { foo: 10, bar: "hello!" };

let foo = getProperty(x, "foo"); // number
let bar = getProperty(x, "bar"); // string

let oops = getProperty(x, "wargarbl"); // Error! "wargarbl" is not "foo" | "bar"

setProperty(x, "foo", "string"); // Error!, string expected number
```

## Mapped Types

One common task is to take an existing type and make each of its properties entirely optional.
Let's say we have a `Person:

```ts
interface Person {
    name: string;
    age: number;
    location: string;
}
```

A partial version of it would be:

```ts
interface PartialPerson {
    name?: string;
    age?: number;
    location?: string;
}
```

with Mapped types, `PartialPerson` can be written as a generalized transformation on the type `Person` as:

```ts
type Partial<T> = {
    [P in keyof T]?: T[P];
};

type PartialPerson = Partial<Person>;
```

Mapped types are produced by taking a union of literal types, and computing a set of properties for a new object type.
They're like [list comprehensions in Python](https://docs.python.org/2/tutorial/datastructures.html#nested-list-comprehensions), but instead of producing new elements in a list, they produce new properties in a type.

In addition to `Partial`, Mapped Types can express many useful transformations on types:

```ts
// Keep types the same, but make each property to be read-only.
type Readonly<T> = {
    readonly [P in keyof T]: T[P];
};

// Same property names, but make the value a promise instead of a concrete one
type Deferred<T> = {
    [P in keyof T]: Promise<T[P]>;
};

// Wrap proxies around properties of T
type Proxify<T> = {
    [P in keyof T]: { get(): T[P]; set(v: T[P]): void }
};
```

## `Partial`, `Readonly`, `Record`, and `Pick`

`Partial` and `Readonly`, as described earlier, are very useful constructs.
You can use them to describe some common JS routines like:

```ts
function assign<T>(obj: T, props: Partial<T>): void;
function freeze<T>(obj: T): Readonly<T>;
```

Because of that, they are now included by default in the standard library.

We're also including two other utility types as well: `Record` and `Pick`.

```ts
// From T pick a set of properties K
declare function pick<T, K extends keyof T>(obj: T, ...keys: K[]): Pick<T, K>;

const nameAndAgeOnly = pick(person, "name", "age");  // { name: string, age: number }
```

```ts
// For every properties K of type T, transform it to U
function mapObject<K extends string | number, T, U>(obj: Record<K, T>, f: (x: T) => U): Record<K, U>

const names = { foo: "hello", bar: "world", baz: "bye" };
const lengths = mapObject(names, s => s.length);  // { foo: number, bar: number, baz: number }
```

## Object Spread and Rest

TypeScript 2.1 brings support for [ES2017 Spread and Rest](https://github.com/sebmarkbage/ecmascript-rest-spread).

Similar to array spread, spreading an object can be handy to get a shallow copy:

```ts
let copy = { ...original };
```

Similarly, you can merge several different objects.
In the following example, `merged` will have properties from `foo`, `bar`, and `baz`.

```ts
let merged = { ...foo, ...bar, ...baz };
```

You can also override existing properties and add new ones:

```ts
let obj = { x: 1, y: "string" };
var newObj = {...obj, z: 3, y: 4}; // { x: number, y: number, z: number }
```

The order of specifying spread operations determines what properties end up in the resulting object;
properties in later spreads "win out" over previously created properties.

Object rests are the dual of object spreads, in that they can extract any extra properties that don't get picked up when destructuring an element:

```ts
let obj = { x: 1, y: 1, z: 1 };
let { z, ...obj1 } = obj;
obj1; // {x: number, y: number};
```

## Downlevel Async Functions

This feature was supported before TypeScript 2.1, but only when targeting ES6/ES2015.
TypeScript 2.1 brings the capability to ES3 and ES5 run-times, meaning you'll be free to take advantage of it no matter what environment you're using.

> Note: first, we need to make sure our run-time has an ECMAScript-compliant `Promise` available globally.
> That might involve grabbing [a polyfill](https://github.com/stefanpenner/es6-promise) for `Promise`, or relying on one that you might have in the run-time that you're targeting.
> We also need to make sure that TypeScript knows `Promise` exists by setting your `lib` flag to something like `"dom", "es2015"` or `"dom", "es2015.promise", "es5"`

##### Example

##### tsconfig.json

```json
{
    "compilerOptions": {
        "lib": ["dom", "es2015.promise", "es5"]
    }
}
```

##### dramaticWelcome.ts

```ts
function delay(milliseconds: number) {
    return new Promise<void>(resolve => {
        setTimeout(resolve, milliseconds);
    });
}

async function dramaticWelcome() {
    console.log("Hello");

    for (let i = 0; i < 3; i++) {
        await delay(500);
        console.log(".");
    }

    console.log("World!");
}

dramaticWelcome();
```

Compiling and running the output should result in the correct behavior on an ES3/ES5 engine.

## Support for external helpers library (`tslib`)

TypeScript injects a handful of helper functions such as `__extends` for inheritance, `__assign` for spread operator in object literals and JSX elements, and `__awaiter` for async functions.

Previously there were two options:

 1. inject helpers in *every* file that needs them, or
 2. no helpers at all with `--noEmitHelpers`.

The two options left more to be desired;
bundling the helpers in every file was a pain point for customers trying to keep their package size small.
And not including helpers, meant customers had to maintain their own helpers library.

TypeScript 2.1 allows for including these files in your project once in a separate module, and the compiler will emit imports to them as needed.

First, install the [`tslib`](https://github.com/Microsoft/tslib) utility library:

```sh
npm install tslib
```

Second, compile your files using `--importHelpers`:

```sh
tsc --module commonjs --importHelpers a.ts
```

So given the following input, the resulting `.js` file will include an import to `tslib` and use the `__assign` helper from it instead of inlining it.

```ts
export const o = { a: 1, name: "o" };
export const copy = { ...o };
```

```js
"use strict";
var tslib_1 = require("tslib");
exports.o = { a: 1, name: "o" };
exports.copy = tslib_1.__assign({}, exports.o);
```

## Untyped imports

TypeScript has traditionally been overly strict about how you can import modules.
This was to avoid typos and prevent users from using modules incorrectly.

However, a lot of the time, you might just want to import an existing module that may not have its own `.d.ts` file.
Previously this was an error.
Starting with TypeScript 2.1 this is now much easier.

With TypeScript 2.1, you can import a JavaScript module without needing a type declaration.
A type declaration (such as `declare module "foo" { ... }` or `node_modules/@types/foo`) still takes priority if it exists.

An import to a module with no declaration file will still be flagged as an error under `--noImplicitAny`.

##### Example

```ts
// Succeeds if `node_modules/asdf/index.js` exists, or if `node_modules/asdf/package.json` defines a valid "main" entry point
import { x } from "asdf";
```

## Support for `--target ES2016`, `--target ES2017` and `--target ESNext`

TypeScript 2.1 supports three new target values `--target ES2016`, `--target ES2017` and `--target ESNext`.

Using target `--target ES2016` will instruct the compiler not to transform ES2016-specific features, e.g. `**` operator.

Similarly, `--target ES2017` will instruct the compiler not to transform ES2017-specific features like `async`/`await`.

`--target ESNext` targets latest supported [ES proposed features](https://github.com/tc39/proposals).

## Improved `any` Inference

Previously, if TypeScript couldn't figure out the type of a variable, it would choose the `any` type.

```ts
let x;      // implicitly 'any'
let y = []; // implicitly 'any[]'

let z: any; // explicitly 'any'.
```

With TypeScript 2.1, instead of just choosing `any`, TypeScript will infer types based on what you end up assigning later on.

This is only enabled if `--noImplicitAny` is set.

##### Example

```ts
let x;

// You can still assign anything you want to 'x'.
x = () => 42;

// After that last assignment, TypeScript 2.1 knows that 'x' has type '() => number'.
let y = x();

// Thanks to that, it will now tell you that you can't add a number to a function!
console.log(x + y);
//          ~~~~~
// Error! Operator '+' cannot be applied to types '() => number' and 'number'.

// TypeScript still allows you to assign anything you want to 'x'.
x = "Hello world!";

// But now it also knows that 'x' is a 'string'!
x.toLowerCase();
```

The same sort of tracking is now also done for empty arrays.

A variable declared with no type annotation and an initial value of `[]` is considered an implicit `any[]` variable.
However, each subsequent `x.push(value)`, `x.unshift(value)` or `x[n] = value` operation *evolves* the type of the variable in accordance with what elements are added to it.

``` ts
function f1() {
    let x = [];
    x.push(5);
    x[1] = "hello";
    x.unshift(true);
    return x;  // (string | number | boolean)[]
}

function f2() {
    let x = null;
    if (cond()) {
        x = [];
        while (cond()) {
            x.push("hello");
        }
    }
    return x;  // string[] | null
}
```

## Implicit any errors

One great benefit of this is that you'll see *way fewer* implicit `any` errors when running with `--noImplicitAny`.
Implicit `any` errors are only reported when the compiler is unable to know the type of a variable without a type annotation.

##### Example

``` ts
function f3() {
    let x = [];  // Error: Variable 'x' implicitly has type 'any[]' in some locations where its type cannot be determined.
    x.push(5);
    function g() {
        x;    // Error: Variable 'x' implicitly has an 'any[]' type.
    }
}
```

## Better inference for literal types

String, numeric and boolean literal types (e.g. `"abc"`, `1`, and `true`) were previously inferred only in the presence of an explicit type annotation.
Starting with TypeScript 2.1, literal types are *always* inferred for `const` variables and `readonly` properties.

The type inferred for a `const` variable or `readonly` property without a type annotation is the type of the literal initializer.
The type inferred for a `let` variable, `var` variable, parameter, or non-`readonly` property with an initializer and no type annotation is the widened literal type of the initializer.
Where the widened type for a string literal type is `string`, `number` for numeric literal types, `boolean` for `true` or `false` and the containing enum for enum literal types.

##### Example

```ts
const c1 = 1;  // Type 1
const c2 = c1;  // Type 1
const c3 = "abc";  // Type "abc"
const c4 = true;  // Type true
const c5 = cond ? 1 : "abc";  // Type 1 | "abc"

let v1 = 1;  // Type number
let v2 = c2;  // Type number
let v3 = c3;  // Type string
let v4 = c4;  // Type boolean
let v5 = c5;  // Type number | string
```

Literal type widening can be controlled through explicit type annotations.
Specifically, when an expression of a literal type is inferred for a const location without a type annotation, that `const` variable gets a widening literal type inferred.
But when a `const` location has an explicit literal type annotation, the `const` variable gets a non-widening literal type.

##### Example

```ts
const c1 = "hello";  // Widening type "hello"
let v1 = c1;  // Type string

const c2: "hello" = "hello";  // Type "hello"
let v2 = c2;  // Type "hello"
```

## Use returned values from super calls as 'this'

In ES2015, constructors which return an object implicitly substitute the value of `this` for any callers of `super()`.
As a result, it is necessary to capture any potential return value of `super()` and replace it with `this`.
This change enables working with [Custom Elements](https://w3c.github.io/webcomponents/spec/custom/#htmlelement-constructor), which takes advantage of this to initialize browser-allocated elements with user-written constructors.

##### Example

```ts
class Base {
    x: number;
    constructor() {
        // return a new object other than `this`
        return {
            x: 1,
        };
    }
}

class Derived extends Base {
    constructor() {
        super();
        this.x = 2;
    }
}
```

Generates:

```js
var Derived = (function (_super) {
    __extends(Derived, _super);
    function Derived() {
        var _this = _super.call(this) || this;
        _this.x = 2;
        return _this;
    }
    return Derived;
}(Base));
```

> This change entails a break in the behavior of extending built-in classes like `Error`, `Array`, `Map`, etc.. Please see the [extending built-ins breaking change documentation](https://github.com/Microsoft/TypeScript-wiki/blob/master/Breaking-Changes.md#extending-built-ins-like-error-array-and-map-may-no-longer-work) for more details.

## Configuration inheritance

Often a project has multiple output targets, e.g. `ES5` and `ES2015`, debug and production or `CommonJS` and `System`;
Just a few configuration options change between these two targets, and maintaining multiple `tsconfig.json` files can be a hassle.

TypeScript 2.1 supports inheriting configuration using `extends`, where:

* `extends` is a new top-level property in `tsconfig.json` (alongside `compilerOptions`, `files`, `include`, and `exclude`).
* The value of `extends` must be a string containing a path to another configuration file to inherit from.
* The configuration from the base file are loaded first, then overridden by those in the inheriting config file.
* Circularity between configuration files is not allowed.
* `files`, `include` and `exclude` from the inheriting config file *overwrite* those from the base config file.
* All relative paths found in the configuration file will be resolved relative to the configuration file they originated in.

##### Example

`configs/base.json`:

```json
{
  "compilerOptions": {
    "noImplicitAny": true,
    "strictNullChecks": true
  }
}
```

`tsconfig.json`:

```json
{
  "extends": "./configs/base",
  "files": [
    "main.ts",
    "supplemental.ts"
  ]
}
```

`tsconfig.nostrictnull.json`:

```json
{
  "extends": "./tsconfig",
  "compilerOptions": {
    "strictNullChecks": false
  }
}
```

## New `--alwaysStrict`

Invoking the compiler with `--alwaysStrict` causes:

1. Parses all the code in strict mode.
2. Writes `"use strict";` directive atop every generated file.

Modules are parsed automatically in strict mode.
The new flag is recommended for non-module code.

# TypeScript 2.0

## Null- and undefined-aware types

TypeScript has two special types, Null and Undefined, that have the values `null` and `undefined` respectively.
Previously it was not possible to explicitly name these types, but `null` and `undefined` may now be used as type names regardless of type checking mode.

The type checker previously considered `null` and `undefined` assignable to anything.
Effectively, `null` and `undefined` were valid values of *every* type and it wasn't possible to specifically exclude them (and therefore not possible to detect erroneous use of them).

### `--strictNullChecks`

`--strictNullChecks` switches to a new strict null checking mode.

In strict null checking mode, the `null` and `undefined` values are *not* in the domain of every type and are only assignable to themselves and `any` (the one exception being that `undefined` is also assignable to `void`).
So, whereas `T` and `T | undefined` are considered synonymous in regular type checking mode (because `undefined` is considered a subtype of any `T`), they are different types in strict type checking mode, and only `T | undefined` permits `undefined` values. The same is true for the relationship of `T` to `T | null`.

#### Example

```ts
// Compiled with --strictNullChecks
let x: number;
let y: number | undefined;
let z: number | null | undefined;
x = 1;  // Ok
y = 1;  // Ok
z = 1;  // Ok
x = undefined;  // Error
y = undefined;  // Ok
z = undefined;  // Ok
x = null;  // Error
y = null;  // Error
z = null;  // Ok
x = y;  // Error
x = z;  // Error
y = x;  // Ok
y = z;  // Error
z = x;  // Ok
z = y;  // Ok
```

### Assigned-before-use checking

In strict null checking mode the compiler requires every reference to a local variable of a type that doesn't include `undefined` to be preceded by an assignment to that variable in every possible preceding code path.

#### Example

```ts
// Compiled with --strictNullChecks
let x: number;
let y: number | null;
let z: number | undefined;
x;  // Error, reference not preceded by assignment
y;  // Error, reference not preceded by assignment
z;  // Ok
x = 1;
y = null;
x;  // Ok
y;  // Ok
```

The compiler checks that variables are definitely assigned by performing *control flow based type analysis*. See later for further details on this topic.

### Optional parameters and properties

Optional parameters and properties automatically have `undefined` added to their types, even when their type annotations don't specifically include `undefined`.
For example, the following two types are identical:

```ts
// Compiled with --strictNullChecks
type T1 = (x?: number) => string;              // x has type number | undefined
type T2 = (x?: number | undefined) => string;  // x has type number | undefined
```

### Non-null and non-undefined type guards

A property access or a function call produces a compile-time error if the object or function is of a type that includes `null` or `undefined`. 
However, type guards are extended to support non-null and non-undefined checks. 

#### Example

```ts
// Compiled with --strictNullChecks
declare function f(x: number): string;
let x: number | null | undefined;
if (x) {
    f(x);  // Ok, type of x is number here
}
else {
    f(x);  // Error, type of x is number? here
}
let a = x != null ? f(x) : "";  // Type of a is string
let b = x && f(x);  // Type of b is string | 0 | null | undefined
```

Non-null and non-undefined type guards may use the `==`, `!=`, `===`, or `!==` operator to compare to `null` or `undefined`, as in `x != null` or `x === undefined`.
The effects on subject variable types accurately reflect JavaScript semantics (e.g. double-equals operators check for both values no matter which one is specified whereas triple-equals only checks for the specified value).

### Dotted names in type guards

Type guards previously only supported checking local variables and parameters. 
Type guards now support checking "dotted names" consisting of a variable or parameter name followed one or more property accesses. 

#### Example

```ts
interface Options {
    location?: {
        x?: number;
        y?: number;
    };
}

function foo(options?: Options) {
    if (options && options.location && options.location.x) {
        const x = options.location.x;  // Type of x is number
    }
}
```

Type guards for dotted names also work with user defined type guard functions and the `typeof` and `instanceof` operators and do not depend on the `--strictNullChecks` compiler option.

A type guard for a dotted name has no effect following an assignment to any part of the dotted name.
For example, a type guard for `x.y.z` will have no effect following an assignment to `x`, `x.y`, or `x.y.z`.

### Expression operators

Expression operators permit operand types to include `null` and/or `undefined` but always produce values of non-null and non-undefined types.

```ts
// Compiled with --strictNullChecks
function sum(a: number | null, b: number | null) {
    return a + b;  // Produces value of type number
}
```

The `&&` operator adds `null` and/or `undefined` to the type of the right operand depending on which are present in the type of the left operand, and the `||` operator removes both `null` and `undefined` from the type of the left operand in the resulting union type.

```ts
// Compiled with --strictNullChecks
interface Entity {
    name: string;
}
let x: Entity | null;
let s = x && x.name;  // s is of type string | null
let y = x || { name: "test" };  // y is of type Entity
```

### Type widening

The `null` and `undefined` types are *not* widened to `any` in strict null checking mode.

```ts
let z = null;  // Type of z is null
```

In regular type checking mode the inferred type of `z` is `any` because of widening, but in strict null checking mode the inferred type of `z` is `null` (and therefore, absent a type annotation, `null` is the only possible value for `z`).

### Non-null assertion operator

A new `!` post-fix expression operator may be used to assert that its operand is non-null and non-undefined in contexts where the type checker is unable to conclude that fact.
Specifically, the operation `x!` produces a value of the type of `x` with `null` and `undefined` excluded.
Similar to type assertions of the forms `<T>x` and `x as T`, the `!` non-null assertion operator is simply removed in the emitted JavaScript code.

```ts
// Compiled with --strictNullChecks
function validateEntity(e?: Entity) {
    // Throw exception if e is null or invalid entity
}

function processEntity(e?: Entity) {
    validateEntity(e);
    let s = e!.name;  // Assert that e is non-null and access name
}
```

### Compatibility

The new features are designed such that they can be used in both strict null checking mode and regular type checking mode.
In particular, the `null` and `undefined` types are automatically erased from union types in regular type checking mode (because they are subtypes of all other types), and the `!` non-null assertion expression operator is permitted but has no effect in regular type checking mode. Thus, declaration files that are updated to use null- and undefined-aware types can still be used in regular type checking mode for backwards compatibility.

In practical terms, strict null checking mode requires that all files in a compilation are null- and undefined-aware. 

## Control flow based type analysis

TypeScript 2.0 implements a control flow-based type analysis for local variables and parameters.
Previously, the type analysis performed for type guards was limited to `if` statements and `?:` conditional expressions and didn't include effects of assignments and control flow constructs such as `return` and `break` statements.
With TypeScript 2.0, the type checker analyses all possible flows of control in statements and expressions to produce the most specific type possible (the *narrowed type*) at any given location for a local variable or parameter that is declared to have a union type.

#### Example

```ts
function foo(x: string | number | boolean) {
    if (typeof x === "string") {
        x; // type of x is string here
        x = 1;
        x; // type of x is number here
    }
    x; // type of x is number | boolean here
}

function bar(x: string | number) {
    if (typeof x === "number") {
        return;
    }
    x; // type of x is string here
}
```

Control flow based type analysis is particuarly relevant in `--strictNullChecks` mode because nullable types are represented using union types:

```ts
function test(x: string | null) {
    if (x === null) {
        return;
    }
    x; // type of x is string in remainder of function
}
```

Furthermore, in `--strictNullChecks` mode, control flow based type analysis includes *definite assignment analysis* for local variables of types that don't permit the value `undefined`.

```ts
function mumble(check: boolean) {
    let x: number; // Type doesn't permit undefined
    x; // Error, x is undefined
    if (check) {
        x = 1;
        x; // Ok
    }
    x; // Error, x is possibly undefined
    x = 2;
    x; // Ok
}
```

## Tagged union types

TypeScript 2.0 implements support for tagged (or discriminated) union types. 
Specifically, the TS compiler now support type guards that narrow union types based on tests of a discriminant property and furthermore extend that capability to `switch` statements.

#### Example

```ts
interface Square {
    kind: "square";
    size: number;
}

interface Rectangle {
    kind: "rectangle";
    width: number;
    height: number;
}

interface Circle {
    kind: "circle";
    radius: number;
}

type Shape = Square | Rectangle | Circle;

function area(s: Shape) {
    // In the following switch statement, the type of s is narrowed in each case clause
    // according to the value of the discriminant property, thus allowing the other properties
    // of that variant to be accessed without a type assertion.
    switch (s.kind) {
        case "square": return s.size * s.size;
        case "rectangle": return s.width * s.height;
        case "circle": return Math.PI * s.radius * s.radius;
    }
}

function test1(s: Shape) {
    if (s.kind === "square") {
        s;  // Square
    }
    else {
        s;  // Rectangle | Circle
    }
}

function test2(s: Shape) {
    if (s.kind === "square" || s.kind === "rectangle") {
        return;
    }
    s;  // Circle
}
```

A *discriminant property type guard* is an expression of the form `x.p == v`, `x.p === v`, `x.p != v`, or `x.p !== v`, where `p` and `v` are a property and an expression of a string literal type or a union of string literal types.
The discriminant property type guard narrows the type of `x` to those constituent types of `x` that have a discriminant property `p` with one of the possible values of `v`.

Note that we currently only support discriminant properties of string literal types. 
We intend to later add support for boolean and numeric literal types.

## The `never` type

TypeScript 2.0 introduces a new primitive type `never`.
The `never` type represents the type of values that never occur. 
Specifically, `never` is the return type for functions that never return and `never` is the type of variables under type guards that are never true. 

The `never` type has the following characteristics:

* `never` is a subtype of and assignable to every type.
* No type is a subtype of or assignable to `never` (except `never` itself).
* In a function expression or arrow function with no return type annotation, if the function has no `return` statements, or only `return` statements with expressions of type `never`, and if the end point of the function is not reachable (as determined by control flow analysis), the inferred return type for the function is `never`.
* In a function with an explicit `never` return type annotation, all `return` statements (if any) must have expressions of type `never` and the end point of the function must not be reachable.

Because `never` is a subtype of every type, it is always omitted from union types and it is ignored in function return type inference as long as there are other types being returned.

Some examples of functions returning `never`:

```ts
// Function returning never must have unreachable end point
function error(message: string): never {
    throw new Error(message);
}

// Inferred return type is never
function fail() {
    return error("Something failed");
}

// Function returning never must have unreachable end point
function infiniteLoop(): never {
    while (true) {
    }
}
```

Some examples of use of functions returning `never`:

```ts
// Inferred return type is number
function move1(direction: "up" | "down") {
    switch (direction) {
        case "up":
            return 1;
        case "down":
            return -1; 
    }
    return error("Should never get here");
}

// Inferred return type is number
function move2(direction: "up" | "down") {
    return direction === "up" ? 1 :
        direction === "down" ? -1 :
        error("Should never get here");
}

// Inferred return type is T
function check<T>(x: T | undefined) {
    return x || error("Undefined value");
}
```

Because `never` is assignable to every type, a function returning `never` can be used when a callback returning a more specific type is required:

```ts
function test(cb: () => string) {
    let s = cb();
    return s;
}

test(() => "hello");
test(() => fail());
test(() => { throw new Error(); })
```

## Read-only properties and index signatures

A property or index signature can now be declared with the `readonly` modifier is considered read-only.

Read-only properties may have initializers and may be assigned to in constructors within the same class declaration, but otherwise assignments to read-only properties are disallowed.

In addition, entities are *implicitly* read-only in several situations:

* A property declared with a `get` accessor and no `set` accessor is considered read-only.
* In the type of an enum object, enum members are considered read-only properties.
* In the type of a module object, exported `const` variables are considered read-only properties.
* An entity declared in an `import` statement is considered read-only.
* An entity accessed through an ES2015 namespace import is considered read-only (e.g. `foo.x` is read-only when `foo` is declared as `import * as foo from "foo"`).

#### Example

```ts
interface Point {
    readonly x: number;
    readonly y: number;
}

var p1: Point = { x: 10, y: 20 };
p1.x = 5;  // Error, p1.x is read-only

var p2 = { x: 1, y: 1 };
var p3: Point = p2;  // Ok, read-only alias for p2
p3.x = 5;  // Error, p3.x is read-only
p2.x = 5;  // Ok, but also changes p3.x because of aliasing
```

```ts
class Foo {
    readonly a = 1;
    readonly b: string;
    constructor() {
        this.b = "hello";  // Assignment permitted in constructor
    }
}
```

```ts
let a: Array<number> = [0, 1, 2, 3, 4];
let b: ReadonlyArray<number> = a;
b[5] = 5;      // Error, elements are read-only
b.push(5);     // Error, no push method (because it mutates array)
b.length = 3;  // Error, length is read-only
a = b;         // Error, mutating methods are missing
```

## Specifying the type of `this` for functions

Following up on specifying the type of `this` in a class or an interface, functions and methods can now declare the type of `this` they expect.

By default the type of `this` inside a function is `any`.
Starting with TypeScript 2.0, you can provide an explicit `this` parameter.
`this` parameters are fake parameters that come first in the parameter list of a function:

```ts
function f(this: void) {
    // make sure `this` is unusable in this standalone function
}
```

### `this` parameters in callbacks

Libraries can also use `this` parameters to declare how callbacks will be invoked.

#### Example

```ts
interface UIElement {
    addClickListener(onclick: (this: void, e: Event) => void): void;
}
```

`this: void` means that `addClickListener` expects `onclick` to be a function that does not require a `this` type.

Now if you annotate calling code with `this`:

```ts
class Handler {
    info: string;
    onClickBad(this: Handler, e: Event) {
        // oops, used this here. using this callback would crash at runtime
        this.info = e.message;
    };
}
let h = new Handler();
uiElement.addClickListener(h.onClickBad); // error!
```

### `--noImplicitThis`

A new flag is also added in TypeScript 2.0 to flag all uses of `this` in functions without an explicit type annotation.

## Glob support in `tsconfig.json`

Glob support is here!! Glob support has been [one of the most requested features](https://github.com/Microsoft/TypeScript/issues/1927).

Glob-like file patterns are supported two properties `"include"` and `"exclude"`.

#### Example

```json
{
    "compilerOptions": {
        "module": "commonjs",
        "noImplicitAny": true,
        "removeComments": true,
        "preserveConstEnums": true,
        "outFile": "../../built/local/tsc.js",
        "sourceMap": true
    },
    "include": [
        "src/**/*"
    ],
    "exclude": [
        "node_modules",
        "**/*.spec.ts"
    ]
}
```

The supported glob wildcards are:

* `*` matches zero or more characters (excluding directory separators)
* `?` matches any one character (excluding directory separators)
* `**/` recursively matches any subdirectory

If a segment of a glob pattern includes only `*` or `.*`, then only files with supported extensions are included (e.g. `.ts`, `.tsx`, and `.d.ts` by default with `.js` and `.jsx` if `allowJs` is set to true).

If the `"files"` and `"include"` are both left unspecified, the compiler defaults to including all TypeScript (`.ts`, `.d.ts` and `.tsx`) files in the containing directory and subdirectories except those excluded using the `"exclude"` property. JS files (`.js` and `.jsx`) are also included if `allowJs` is set to true.

If the `"files"` or `"include"` properties are specified, the compiler will instead include the union of the files included by those two properties.
Files in the directory specified using the `"outDir"` compiler option are always excluded unless explicitly included via the `"files"` property (even when the "`exclude`" property is specified).

Files included using `"include"` can be filtered using the `"exclude"` property.
However, files included explicitly using the `"files"` property are always included regardless of `"exclude"`.
The `"exclude"` property defaults to excluding the `node_modules`, `bower_components`, and `jspm_packages` directories when not specified.

## Module resolution enhancements: BaseUrl, Path mapping, rootDirs and tracing

TypeScript 2.0 provides a set of additional module resolution knops to *inform* the compiler where to find declarations for a given module.

See [Module Resolution](http://www.typescriptlang.org/docs/handbook/module-resolution.html) documentation for more details.

### Base URL

Using a `baseUrl` is a common practice in applications using AMD module loaders where modules are "deployed" to a single folder at run-time.
All module imports with non-relative names are assumed to be relative to the `baseUrl`.

#### Example

```json
{
  "compilerOptions": {
    "baseUrl": "./modules"
  }
}
```

Now imports to `"moduleA"` would be looked up in `./modules/moduleA`

```ts
import A from "moduleA";
```

### Path mapping

Sometimes modules are not directly located under *baseUrl*.
Loaders use a mapping configuration to map module names to files at run-time, see [RequireJs documentation](http://requirejs.org/docs/api.html#config-paths) and [SystemJS documentation](https://github.com/systemjs/systemjs/blob/master/docs/overview.md#map-config).

The TypeScript compiler supports the declaration of such mappings using `"paths"` property in `tsconfig.json` files.

#### Example

For instance, an import to a module `"jquery"` would be translated at runtime to `"node_modules/jquery/dist/jquery.slim.min.js"`.

```json
{
  "compilerOptions": {
    "baseUrl": "./node_modules",
    "paths": {
      "jquery": ["jquery/dist/jquery.slim.min"]
    }
}
```

Using `"paths"` also allow for more sophisticated mappings including multiple fall back locations.
Consider a project configuration where only some modules are available in one location, and the rest are in another.

### Virtual Directories with `rootDirs`

Using 'rootDirs', you can inform the compiler of the *roots* making up this "virtual" directory;
and thus the compiler can resolve relative modules imports within these "virtual" directories *as if* were merged together in one directory.

#### Example

Given this project structure:

```tree
 src
 └── views
     └── view1.ts (imports './template1')
     └── view2.ts

 generated
 └── templates
         └── views
             └── template1.ts (imports './view2')
```

A build step will copy the files in `/src/views` and `/generated/templates/views` to the same directory in the output.
At run-time, a view can expect its template to exist next to it, and thus should import it using a relative name as `"./template"`.

`"rootDirs"` specify a list of *roots* whose contents are expected to merge at run-time.
So following our example, the `tsconfig.json` file should look like:

```json
{
  "compilerOptions": {
    "rootDirs": [
      "src/views",
      "generated/templates/views"
    ]
  }
}
```

### Tracing module resolution

`--traceResolution` offers a handy way to understand how modules have been resolved by the compiler. 

```shell
tsc --traceResolution
```

## Shorthand ambient module declarations

If you don't want to take the time to write out declarations before using a new module, you can now just use a shorthand declaration to get started quickly.

#### declarations.d.ts
```ts
declare module "hot-new-module";
```

All imports from a shorthand module will have the any type.

```ts
import x, {y} from "hot-new-module";
x(y);
```

## Wildcard character in module names

Importing none-code resources using module loaders extension (e.g. [AMD](https://github.com/amdjs/amdjs-api/blob/master/LoaderPlugins.md) or [SystemJS](https://github.com/systemjs/systemjs/blob/master/docs/creating-plugins.md)) has not been easy before;
previously an ambient module declaration had to be defined for each resource.

TypeScript 2.0 supports the use of the wildcard character (`*`) to declare a "family" of module names;
this way, a declaration is only required once for an extension, and not for every resource.

#### Example

```ts
declare module "*!text" {
    const content: string;
    export default content;
}
// Some do it the other way around.
declare module "json!*" {
    const value: any;
    export default value;
}
```

Now you can import things that match `"*!text"` or `"json!*"`.

```ts
import fileContent from "./xyz.txt!text";
import data from "json!http://example.com/data.json";
console.log(data, fileContent);
```

Wildcard module names can be even more useful when migrating from an un-typed code base.
Combined with Shorthand ambient module declarations, a set of modules can be easily declared as `any`.

#### Example

```ts
declare module "myLibrary/*";
```

All imports to any module under `myLibrary` would be considered to have the type `any` by the compiler;
thus, shutting down any checking on the shapes or types of these modules.

```ts
import { readFile } from "myLibrary/fileSystem/readFile`;

readFile(); // readFile is 'any'
```

## Support for UMD module definitions

Some libraries are designed to be used in many module loaders, or with no module loading (global variables).
These are known as [UMD](https://github.com/umdjs/umd) or [Isomorphic](http://isomorphic.net) modules.
These libraries can be accessed through either an import or a global variable.

For example:

##### math-lib.d.ts

```ts
export const isPrime(x: number): boolean;
export as namespace mathLib;
```

The library can then be used as an import within modules:

```ts
import { isPrime } from "math-lib";
isPrime(2);
mathLib.isPrime(2); // ERROR: can't use the global definition from inside a module
```

It can also be used as a global variable, but only inside of a script.
(A script is a file with no imports or exports.)

```ts
mathLib.isPrime(2);
```

## Optional class properties

Optional properties and methods can now be declared in classes, similar to what is already permitted in interfaces. 

#### Example

```ts
class Bar {
    a: number;
    b?: number;
    f() {
        return 1;
    }
    g?(): number;  // Body of optional method can be omitted
    h?() {
        return 2;
    }
}
```

When compiled in `--strictNullChecks` mode, optional properties and methods automatically have `undefined` included in their type. Thus, the `b` property above is of type `number | undefined` and the `g` method above is of type `(() => number) | undefined`. 
Type guards can be used to strip away the `undefined` part of the type:

```ts
function test(x: Bar) {
    x.a;  // number
    x.b;  // number | undefined
    x.f;  // () => number
    x.g;  // (() => number) | undefined
    let f1 = x.f();            // number
    let g1 = x.g && x.g();     // number | undefined
    let g2 = x.g ? x.g() : 0;  // number
}
```

## Private and Protected Constructors

A class constructor may be marked `private` or `protected`.
A class with private constructor cannot be instantiated outside the class body, and cannot be extended.
A class with protected constructor cannot be instantiated outside the class body, but can be extended.

#### Example

```ts
class Singleton {
    private static instance: Singleton;

    private constructor() { }

    static getInstance() {
        if (!Singleton.instance) {
            Singleton.instance = new Singleton();
        }
        return Singleton.instance;
    } 
}

let e = new Singleton(); // Error: constructor of 'Singleton' is private.
let v = Singleton.getInstance();
```

## Abstract properties and accessors

An abstract class can declare abstract properties and/or accessors.
Any sub class will need to declare the abstract properties or be marked as abstract.
Abstract properties cannot have an initializer.
Abstract accessors cannot have bodies.

#### Example

```ts
abstract class Base {
    abstract name: string;
    abstract get value();
    abstract set value(v: number);
}

class Derived extends Base {
    name = "derived";

    value = 1;
}
```

## Implicit index signatures

An object literal type is now assignable to a type with an index signature if all known properties in the object literal are assignable to that index signature. This makes it possible to pass a variable that was initialized with an object literal as parameter to a function that expects a map or dictionary:

```ts
function httpService(path: string, headers: { [x: string]: string }) { }

const headers = {
    "Content-Type": "application/x-www-form-urlencoded"
};

httpService("", { "Content-Type": "application/x-www-form-urlencoded" });  // Ok
httpService("", headers);  // Now ok, previously wasn't
```

## Including built-in type declarations with `--lib`

Getting to ES6/ES2015 built-in API declarations were only limited to `target: ES6`.
Enter `--lib`; with `--lib` you can specify a list of built-in API declaration groups that you can chose to include in your project.
For instance, if you expect your runtime to have support for `Map`, `Set` and `Promise` (e.g. most evergreen browsers today), just include `--lib es2015.collection,es2015.promise`.
Similarly you can exclude declarations you do not want to include in your project, e.g. DOM if you are working on a node project using `--lib es5,es6`.

Here is a list of available API groups:

* dom
* webworker
* es5
* es6 / es2015
* es2015.core
* es2015.collection
* es2015.iterable
* es2015.promise
* es2015.proxy
* es2015.reflect
* es2015.generator
* es2015.symbol
* es2015.symbol.wellknown
* es2016
* es2016.array.include
* es2017
* es2017.object
* es2017.sharedmemory
* scripthost


#### Example

```bash
tsc --target es5 --lib es5,es2015.promise
```

```json
"compilerOptions": {
    "lib": ["es5", "es2015.promise"]
}
```

## Flag unused declarations with `--noUnusedParameters` and `--noUnusedLocals` 

TypeScript 2.0 has two new flags to help you maintain a clean code base.
`--noUnusedParameters` flags any unused function or method parameters errors. 
`--noUnusedLocals` flags any unused local (un-exported) declaration like variables, functions, classes, imports, etc...
Also, unused private members of a class would be flagged as errors under `--noUnusedLocals`.


#### Example
```ts
import B, { readFile } from "./b";
//     ^ Error: `B` declared but never used
readFile();


export function write(message: string, args: string[]) {
    //                                 ^^^^  Error: 'arg' declared but never used.
    console.log(message);
}
```

Parameters declaration with names starting with `_` are exempt from the unused parameter checking.
e.g.:

```ts
function returnNull(_a) { // OK
    return null;
}
```

## Module identifiers allow for `.js` extension

Before TypeScript 2.0, a module identifier was always assumed to be extension-less;
for instance, given an import as `import d from "./moduleA.js"`, the compiler looked up the definition of `"moduleA.js"` in `./moduleA.js.ts` or `./moduleA.js.d.ts`.
This made it hard to use bundling/loading tools like [SystemJS](https://github.com/systemjs/systemjs) that expect URI's in their module identifier.

With TypeScript 2.0, the compiler will look up definition of `"moduleA.js"` in  `./moduleA.ts` or `./moduleA.d.ts`.

## Support 'target : es5' with 'module: es6'

Previously flagged as an invalid flag combination, `target: es5` and 'module: es6' is now supported.
This should facilitate using ES2015-based tree shakers like [rollup](https://github.com/rollup/rollup).

## Trailing commas in function parameter and argument lists

Trailing comma in function parameter and argument lists are now allowed.
This is an implementation for a [Stage-3 ECMAScript proposal](https://jeffmo.github.io/es-trailing-function-commas/) that emits down to valid ES3/ES5/ES6.

#### Example
```ts
function foo(
  bar: Bar, 
  baz: Baz, // trailing commas are OK in parameter lists
) {
  // Implementation...
}

foo(
  bar,
  baz, // and in argument lists
);
```

## New `--skipLibCheck`

TypeScript 2.0 adds a new `--skipLibCheck` compiler option that causes type checking of declaration files (files with extension `.d.ts`) to be skipped.
When a program includes large declaration files, the compiler spends a lot of time type checking declarations that are already known to not contain errors, and compile times may be significantly shortened by skipping declaration file type checks.

Since declarations in one file can affect type checking in other files, some errors may not be detected when `--skipLibCheck` is specified.
For example, if a non-declaration file augments a type declared in a declaration file, errors may result that are only reported when the declaration file is checked. 
However, in practice such situations are rare.

## Allow duplicate identifiers across declarations

This has been one common source of duplicate definition errors.
Multiple declaration files defining the same members on interfaces.

TypeScript 2.0 relaxes this constraint and allows duplicate identifiers across blocks, as long as they have *identical* types.

Within the same block duplicate definitions are still disallowed.

#### Example

```ts
interface Error {
    stack?: string;
}


interface Error {
    code?: string;
    path?: string;
    stack?: string;  // OK
}

```

## New `--declarationDir`

`--declarationDir` allows for generating declaration files in a different location than JavaScript files.


# TypeScript 1.8

## Type parameters as constraints

With TypeScript 1.8 it becomes possible for a type parameter constraint to reference type parameters from the same type parameter list. Previously this was an error. This capability is usually referred to as [F-Bounded Polymorphism](https://en.wikipedia.org/wiki/Bounded_quantification#F-bounded_quantification).

##### Example

```ts
function assign<T extends U, U>(target: T, source: U): T {
    for (let id in source) {
        target[id] = source[id];
    }
    return target;
}

let x = { a: 1, b: 2, c: 3, d: 4 };
assign(x, { b: 10, d: 20 });
assign(x, { e: 0 });  // Error
```

## Control flow analysis errors

TypeScript 1.8 introduces control flow analysis to help catch common errors that users tend to run into.
Read on to get more details, and check out these errors in action: 

![cfa](https://cloud.githubusercontent.com/assets/8052307/5210657/c5ae0f28-7585-11e4-97d8-86169ef2a160.gif)

### Unreachable code

Statements guaranteed to not be executed at run time are now correctly flagged as unreachable code errors. For instance, statements following unconditional `return`, `throw`, `break` or `continue` statements are considered unreachable. Use `--allowUnreachableCode` to disable unreachable code detection and reporting.

##### Example

Here's a simple example of an unreachable code error:

```ts
function f(x) {
    if (x) {
       return true;
    }
    else {
       return false;
    }

    x = 0; // Error: Unreachable code detected.
}
```

A more common error that this feature catches is adding a newline after a `return` statement:

```ts
function f() {
    return            // Automatic Semicolon Insertion triggered at newline
    {
        x: "string"   // Error: Unreachable code detected.
    }
}
```

Since JavaScript automatically terminates the `return` statement at the end of the line, the object literal becomes a block.


### Unused labels

Unused labels are also flagged. Just like unreachable code checks, these are turned on by default; use `--allowUnusedLabels` to stop reporting these errors.

##### Example

```ts
loop: while (x > 0) {  // Error: Unused label.
    x++;
}
```

### Implicit returns

Functions with code paths that do not return a value in JS implicitly return `undefined`. These can now be flagged by the compiler as implicit returns. The check is turned *off* by default; use `--noImplicitReturns` to turn it on.

##### Example

```ts
function f(x) { // Error: Not all code paths return a value.
    if (x) {
        return false;
    }

    // implicitly returns `undefined`
}
```

### Case clause fall-throughs

TypeScript can reports errors for fall-through cases in switch statement where the case clause is non-empty.
This check is turned *off* by default, and can be enabled using `--noFallthroughCasesInSwitch`.

##### Example

With `--noFallthroughCasesInSwitch`, this example will trigger an error:

```ts
switch (x % 2) {
    case 0: // Error: Fallthrough case in switch.
        console.log("even");

    case 1:
        console.log("odd");
        break;
}
```

However, in the following example, no error will be reported because the fall-through case is empty:

```ts
switch (x % 3) {
    case 0:
    case 1:
        console.log("Acceptable");
        break;

    case 2:
        console.log("This is *two much*!");
        break;
}
```


## Stateless Function Components in React

TypeScript now supports [Stateless Function components](https://facebook.github.io/react/docs/reusable-components.html#stateless-functions).
These are lightweight components that easily compose other components:

```ts
// Use parameter destructuring and defaults for easy definition of 'props' type
const Greeter = ({name = 'world'}) => <div>Hello, {name}!</div>;

// Properties get validated
let example = <Greeter name='TypeScript 1.8' />;
```

For this feature and simplified props, be sure to be use the [latest version of react.d.ts](https://github.com/DefinitelyTyped/DefinitelyTyped/tree/master/react).

## Simplified `props` type management in React

In TypeScript 1.8 with the latest version of react.d.ts (see above), we've also greatly simplified the declaration of `props` types.

Specifically:
 * You no longer need to either explicitly declare `ref` and `key` or `extend React.Props`
 * The `ref` and `key` properties will appear with correct types on all components
 * The `ref` property is correctly disallowed on instances of Stateless Function components

## Augmenting global/module scope from modules

Users can now declare any augmentations that they want to make, or that any other consumers already have made, to an existing module.
Module augmentations look like plain old ambient module declarations (i.e. the `declare module "foo" { }` syntax), and are directly nested either your own modules, or in another top level ambient external module.

Furthermore, TypeScript also has the notion of *global* augmentations of the form `declare global { }`.
This allows modules to augment global types such as `Array` if necessary.

The name of a module augmentation is resolved using the same set of rules as module specifiers in `import` and `export` declarations.
The declarations in a module augmentation are merged with any existing declarations the same way they would if they were declared in the same file.

Neither module augmentations nor global augmentations can add new items to the top level scope - they can only "patch" existing declarations.

##### Example

Here `map.ts` can declare that it will internally patch the `Observable` type from `observable.ts` and add the `map` method to it.

```ts
// observable.ts
export class Observable<T> {
    // ...
}
```

```ts
// map.ts
import { Observable } from "./observable";

// Create an augmentation for "./observable"
declare module "./observable" {

    // Augment the 'Observable' class definition with interface merging
    interface Observable<T> {
        map<U>(proj: (el: T) => U): Observable<U>;
    }

}

Observable.prototype.map = /*...*/;
```

```ts
// consumer.ts
import { Observable } from "./observable";
import "./map";

let o: Observable<number>;
o.map(x => x.toFixed());
```

Similarly, the global scope can be augmented from modules using a `declare global` declarations:

##### Example

```ts
// Ensure this is treated as a module.
export {};

declare global {
    interface Array<T> {
        mapToNumbers(): number[];
    }
}

Array.prototype.mapToNumbers = function () { /* ... */ }
```

## String literal types

It's not uncommon for an API to expect a specific set of strings for certain values.
For instance, consider a UI library that can move elements across the screen while controlling the ["easing" of the animation.](https://en.wikipedia.org/wiki/Inbetweening)

```ts
declare class UIElement {
    animate(options: AnimationOptions): void;
}

interface AnimationOptions {
    deltaX: number;
    deltaY: number;
    easing: string; // Can be "ease-in", "ease-out", "ease-in-out"
}
```

However, this is error prone - there is nothing stopping a user from accidentally misspelling one of the valid easing values:

```ts
// No errors
new UIElement().animate({ deltaX: 100, deltaY: 100, easing: "ease-inout" });
```

With TypeScript 1.8, we've introduced string literal types.
These types are written the same way string literals are, but in type positions.

Users can now ensure that the type system will catch such errors.
Here's our new `AnimationOptions` using string literal types:

```ts
interface AnimationOptions {
    deltaX: number;
    deltaY: number;
    easing: "ease-in" | "ease-out" | "ease-in-out";
}

// Error: Type '"ease-inout"' is not assignable to type '"ease-in" | "ease-out" | "ease-in-out"'
new UIElement().animate({ deltaX: 100, deltaY: 100, easing: "ease-inout" });
```

## Improved union/intersection type inference

TypeScript 1.8 improves type inference involving source and target sides that are both union or intersection types.
For example, when inferring from `string | string[]` to `string | T`, we reduce the types to `string[]` and `T`, thus inferring `string[]` for `T`.

##### Example

```ts
type Maybe<T> = T | void;

function isDefined<T>(x: Maybe<T>): x is T {
    return x !== undefined && x !== null;
}

function isUndefined<T>(x: Maybe<T>): x is void {
    return x === undefined || x === null;
}

function getOrElse<T>(x: Maybe<T>, defaultValue: T): T {
    return isDefined(x) ? x : defaultValue;
}

function test1(x: Maybe<string>) {
    let x1 = getOrElse(x, "Undefined");         // string
    let x2 = isDefined(x) ? x : "Undefined";    // string
    let x3 = isUndefined(x) ? "Undefined" : x;  // string
}

function test2(x: Maybe<number>) {
    let x1 = getOrElse(x, -1);         // number
    let x2 = isDefined(x) ? x : -1;    // number
    let x3 = isUndefined(x) ? -1 : x;  // number
}
```

## Concatenate `AMD` and `System` modules with `--outFile`

Specifying `--outFile` in conjunction with `--module amd` or `--module system` will concatenate all modules in the compilation into a single output file containing multiple module closures.

A module name will be computed for each module based on its relative location to `rootDir`.

##### Example
```ts
// file src/a.ts
import * as B from "./lib/b";
export function createA() {
    return B.createB();
}
```

```ts
// file src/lib/b.ts
export function createB() {
    return { };
}
```

Results in:

```js
define("lib/b", ["require", "exports"], function (require, exports) {
    "use strict";
    function createB() {
        return {};
    }
    exports.createB = createB;
});
define("a", ["require", "exports", "lib/b"], function (require, exports, B) {
    "use strict";
    function createA() {
        return B.createB();
    }
    exports.createA = createA;
});
```

## Support for `default` import interop with SystemJS

Module loaders like SystemJS wrap CommonJS modules and expose then as a `default` ES6 import. This makes it impossible to share the definition files between the SystemJS and CommonJS implementation of the module as the module shape looks different based on the loader.

Setting the new compiler flag `--allowSyntheticDefaultImports` indicates that the module loader performs some kind of synthetic default import member creation not indicated in the imported .ts or .d.ts. The compiler will infer the existence of a `default` export that has the shape of the entire module itself.

System modules have this flag on by default.

## Allow captured `let`/`const` in loops

Previously an error, now supported in TypeScript 1.8.
`let`/`const` declarations within loops and captured in functions are now emitted to correctly match `let`/`const` freshness semantics.

##### Example

```ts
let list = [];
for (let i = 0; i < 5; i++) {
    list.push(() => i);
}

list.forEach(f => console.log(f()));
```

is compiled to:

```js
var list = [];
var _loop_1 = function(i) {
    list.push(function () { return i; });
};
for (var i = 0; i < 5; i++) {
    _loop_1(i);
}
list.forEach(function (f) { return console.log(f()); });
```

And results in

```cmd
0
1
2
3
4
```

## Improved checking for `for..in` statements

Previously the type of a `for..in` variable is inferred to `any`; that allowed the compiler to ignore invalid uses within the `for..in` body.

Starting with TypeScript 1.8,:
* The type of a variable declared in a `for..in` statement is implicitly `string`.
* When an object with a numeric index signature of type `T` (such as an array) is indexed by a `for..in` variable of a containing `for..in` statement for an object *with* a numeric index signature and *without* a string index signature (again such as an array), the value produced is of type `T`.

##### Example

```ts
var a: MyObject[];
for (var x in a) {   // Type of x is implicitly string
    var obj = a[x];  // Type of obj is MyObject
}
```

## Modules are now emitted with a `"use strict";` prologue

Modules were always parsed in strict mode as per ES6, but for non-ES6 targets this was not respected in the generated code. Starting with TypeScript 1.8, emitted modules are always in strict mode. This shouldn't have any visible changes in most code as TS considers most strict mode errors as errors at compile time, but it means that some things which used to silently fail at runtime in your TS code, like assigning to `NaN`, will now loudly fail. You can reference the [MDN Article](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Strict_mode) on strict mode for a detailed list of the differences between strict mode and non-strict mode.

## Including `.js` files with `--allowJs`

Often there are external source files in your project that may not be authored in TypeScript.
Alternatively, you might be in the middle of converting a JS code base into TS, but still want to bundle all your JS code into a single file with the output of your new TS code.

`.js` files are now allowed as input to `tsc`.
The TypeScript compiler checks the input `.js` files for syntax errors, and emits valid output based on the `--target` and `--module` flags.
The output can be combined with other `.ts` files as well.
Source maps are still generated for `.js` files just like with `.ts` files.

## Custom JSX factories using `--reactNamespace`

Passing `--reactNamespace <JSX factory Name>` along with `--jsx react` allows for using a different JSX factory from the default `React`.

The new factory name will be used to call `createElement` and `__spread` functions.

##### Example

```ts
import {jsxFactory} from "jsxFactory";

var div = <div>Hello JSX!</div>
```

Compiled with:

```shell
tsc --jsx react --reactNamespace jsxFactory --m commonJS
```

Results in:

```js
"use strict";
var jsxFactory_1 = require("jsxFactory");
var div = jsxFactory_1.jsxFactory.createElement("div", null, "Hello JSX!");
```

## `this`-based type guards

TypeScript 1.8 extends [user-defined type guard functions](#user-defined-type-guard-functions) to class and interface methods.

`this is T` is now valid return type annotation for methods in classes and interfaces.
When used in a type narowing position (e.g. `if` statement), the type of the call expression target object would be narrowed to `T`.

##### Example

```ts

class FileSystemObject {
    isFile(): this is File { return this instanceof File; }
    isDirectory(): this is Directory { return this instanceof Directory;}
    isNetworked(): this is (Networked & this) { return this.networked; }
    constructor(public path: string, private networked: boolean) {}
}

class File extends FileSystemObject {
    constructor(path: string, public content: string) { super(path, false); }
}
class Directory extends FileSystemObject {
    children: FileSystemObject[];
}
interface Networked {
    host: string;
}

let fso: FileSystemObject = new File("foo/bar.txt", "foo");
if (fso.isFile()) {
    fso.content; // fso is File
}
else if (fso.isDirectory()) {
    fso.children; // fso is Directory
}
else if (fso.isNetworked()) {
    fso.host; // fso is networked
}
```

## Official TypeScript NuGet package

Starting with TypeScript 1.8, official NuGet packages are available for the Typescript Compiler (`tsc.exe`) as well as the MSBuild integration (`Microsoft.TypeScript.targets` and `Microsoft.TypeScript.Tasks.dll`).

Stable packages are available here:
* [Microsoft.TypeScript.Compiler](https://www.nuget.org/packages/Microsoft.TypeScript.Compiler/)
* [Microsoft.TypeScript.MSBuild](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/)

Also, a nightly NuGet package to match the [nightly npm package](http://blogs.msdn.com/b/typescript/archive/2015/07/27/introducing-typescript-nightlies.aspx) is available on https://myget.org:

* [TypeScript-Preview](https://www.myget.org/gallery/typescript-preview)

## Prettier error messages from `tsc`

We understand that a ton of monochrome output can be a little difficult on the eyes.
Colors can help discern where a message starts and ends, and these visual clues are important when error output gets overwhelming.

By just passing the `--pretty` command line option, TypeScript gives more colorful output with context about where things are going wrong.

![Showing off pretty error messages in ConEmu](https://raw.githubusercontent.com/wiki/Microsoft/TypeScript/images/new-in-typescript/pretty01.png)

## Colorization of JSX code in VS 2015

With TypeScript 1.8, JSX tags are now classified and colorized in Visual Studio 2015.

![jsx](https://cloud.githubusercontent.com/assets/8052307/12271404/b875c502-b90f-11e5-93d8-c6740be354d1.png)

The classification can be further customized by changing the font and color settings for the `VB XML` color and font settings through `Tools`->`Options`->`Environment`->`Fonts and Colors` page.

## The `--project` (`-p`) flag can now take any file path

The `--project` command line option originally could only take paths to a folder containing a `tsconfig.json`.
Given the different scenarios for build configurations, it made sense to allow `--project` to point to any other compatible JSON file.
For instance, a user might want to target ES2015 with CommonJS modules for Node 5, but ES5 with AMD modules for the browser.
With this new work, users can easily manage two separate build targets using `tsc` alone without having to perform hacky workarounds like placing `tsconfig.json` files in separate directories.

The old behavior still remains the same if given a directory - the compiler will try to find a file in the directory named `tsconfig.json`.

## Allow comments in tsconfig.json

It's always nice to be able to document your configuration!
`tsconfig.json` now accepts single and multi-line comments.

```ts
{
    "compilerOptions": {
        "target": "ES2015", // running on node v5, yaay!
        "sourceMap": true   // makes debugging easier
    },
    /*
     * Excluded files
      */
    "exclude": [
        "file.d.ts"
    ]
}
```

## Support output to IPC-driven files

TypeScript 1.8 allows users to use the `--outFile` argument with special file system entities like named pipes, devices, etc.

As an example, on many Unix-like systems, the standard output stream is accessible by the file `/dev/stdout`.

```shell
tsc foo.ts --outFile /dev/stdout
```

This can be used to pipe output between commands as well.

As an example, we can pipe our emitted JavaScript into a pretty printer like [pretty-js](https://www.npmjs.com/package/pretty-js):

```shell
tsc foo.ts --outFile /dev/stdout | pretty-js
```

## Improved support for `tsconfig.json` in Visual Studio 2015

TypeScript 1.8 allows `tsconfig.json` files in all project types.
This includes ASP.NET v4 projects, *Console Application*, and the *Html Application with TypeScript* project types.
Further, you are no longer limited to a single `tsconfig.json` file but can add multiple, and each will be built as part of the project.
This allows you to separate the configuration for different parts of your application without having to use multiple different projects.

![Showing off tsconfig.json in Visual Studio](https://raw.githubusercontent.com/wiki/Microsoft/TypeScript/images/new-in-typescript/tsconfig-in-vs.png)

We also disable the project properties page when you add a `tsconfig.json` file.
This means that all configuration changes have to be made in the `tsconfig.json` file itself.

### A couple of limitations:

* If you add a `tsconfig.json` file, TypeScript files that are not considered part of that context are not compiled.
* Apache Cordova Apps still have the existing limitation of a single `tsconfig.json` file, which must be in either the root or the `scripts` folder.
* There is no template for `tsconfig.json` in most project types.

# TypeScript 1.7

## `async`/`await` support in ES6 targets (Node v4+)

TypeScript now supports asynchronous functions for engines that have native support for ES6 generators, e.g. Node v4 and above.
Asynchronous functions are prefixed with the `async` keyword; `await` suspends the execution until an asynchronous function return promise is fulfilled and unwraps the value from the `Promise` returned.

##### Example

In the following example, each input element will be printed out one at a time with a 200ms delay:

```ts
"use strict";

// printDelayed is a 'Promise<void>'
async function printDelayed(elements: string[]) {
    for (const element of elements) {
        await delay(200);
        console.log(element);
    }
}

async function delay(milliseconds: number) {
    return new Promise<void>(resolve => {
        setTimeout(resolve, milliseconds);
    });
}

printDelayed(["Hello", "beautiful", "asynchronous", "world"]).then(() => {
    console.log();
    console.log("Printed every element!");
});
```

For more information see [Async Functions](http://blogs.msdn.com/b/typescript/archive/2015/11/03/what-about-async-await.aspx) blog post.

## Support for `--target ES6` with `--module`

TypeScript 1.7 adds `ES6` to the list of options available for the `--module` flag and allows you to specify the module output when targeting `ES6`. This provides more flexibility to target exactly the features you want in specific runtimes.

##### Example

```json
{
    "compilerOptions": {
        "module": "amd",
        "target": "es6"
    }
}
```

## `this`-typing

It is a common pattern to return the current object (i.e. `this`) from a method to create [fluent-style APIs](https://en.wikipedia.org/wiki/Fluent_interface).
For instance, consider the following `BasicCalculator` module:

```ts
export default class BasicCalculator {
    public constructor(protected value: number = 0) { }

    public currentValue(): number {
        return this.value;
    }

    public add(operand: number) {
        this.value += operand;
        return this;
    }

    public subtract(operand: number) {
        this.value -= operand;
        return this;
    }

    public multiply(operand: number) {
        this.value *= operand;
        return this;
    }

    public divide(operand: number) {
        this.value /= operand;
        return this;
    }
}
```

A user could express `2 * 5 + 1` as

```ts
import calc from "./BasicCalculator";

let v = new calc(2)
    .multiply(5)
    .add(1)
    .currentValue();
```

This often opens up very elegant ways of writing code; however, there was a problem for classes that wanted to extend from `BasicCalculator`.
Imagine a user wanted to start writing a `ScientificCalculator`:

```ts
import BasicCalculator from "./BasicCalculator";

export default class ScientificCalculator extends BasicCalculator {
    public constructor(value = 0) {
        super(value);
    }

    public square() {
        this.value = this.value ** 2;
        return this;
    }

    public sin() {
        this.value = Math.sin(this.value);
        return this;
    }
}
```

Because TypeScript used to infer the type `BasicCalculator` for each method in `BasicCalculator` that returned `this`, the type system would forget that it had `ScientificCalculator` whenever using a `BasicCalculator` method.

For instance:

```ts
import calc from "./ScientificCalculator";

let v = new calc(0.5)
    .square()
    .divide(2)
    .sin()    // Error: 'BasicCalculator' has no 'sin' method.
    .currentValue();
```

This is no longer the case - TypeScript now infers `this` to have a special type called `this` whenever inside an instance method of a class.
The `this` type is written as so, and basically means "the type of the left side of the dot in a method call".

The `this` type is also useful with intersection types in describing libraries (e.g. Ember.js) that use mixin-style patterns to describe inheritance:

```ts
interface MyType {
    extend<T>(other: T): this & T;
}
```

## ES7 exponentiation operator

TypeScript 1.7 supports upcoming [ES7/ES2016 exponentiation operators](https://github.com/rwaldron/exponentiation-operator): `**` and `**=`. The operators will be transformed in the output to ES3/ES5 using `Math.pow`.

##### Example

```ts
var x = 2 ** 3;
var y = 10;
y **= 2;
var z =  -(4 ** 3);
```

Will generate the following JavaScript output:

```js
var x = Math.pow(2, 3);
var y = 10;
y = Math.pow(y, 2);
var z = -(Math.pow(4, 3));
```

## Improved checking for destructuring object literal

TypeScript 1.7 makes checking of destructuring patterns with an object literal or array literal initializers less rigid and more intuitive.

When an object literal is contextually typed by the implied type of an object binding pattern:
* Properties with default values in the object binding pattern become optional in the object literal.
* Properties in the object binding pattern that have no match in the object literal are required to have a default value in the object binding pattern and are automatically added to the object literal type.
* Properties in the object literal that have no match in the object binding pattern are an error.

When an array literal is contextually typed by the implied type of an array binding pattern:

* Elements in the array binding pattern that have no match in the array literal are required to have a default value in the array binding pattern and are automatically added to the array literal type.

##### Example

```ts
// Type of f1 is (arg?: { x?: number, y?: number }) => void
function f1({ x = 0, y = 0 } = {}) { }

// And can be called as:
f1();
f1({});
f1({ x: 1 });
f1({ y: 1 });
f1({ x: 1, y: 1 });

// Type of f2 is (arg?: (x: number, y?: number) => void
function f2({ x, y = 0 } = { x: 0 }) { }

f2();
f2({});        // Error, x not optional
f2({ x: 1 });
f2({ y: 1 });  // Error, x not optional
f2({ x: 1, y: 1 });
```

## Support for decorators when targeting ES3

Decorators are now allowed when targeting ES3. TypeScript 1.7 removes the ES5-specific use of `reduceRight` from the `__decorate` helper. The changes also inline calls `Object.getOwnPropertyDescriptor` and `Object.defineProperty` in a backwards-compatible fashion that allows for a to clean up the emit for ES5 and later by removing various repetitive calls to the aforementioned `Object` methods.


# TypeScript 1.6

## JSX support

JSX is an embeddable XML-like syntax. It is meant to be transformed into valid JavaScript, but the semantics of that transformation are implementation-specific. JSX came to popularity with the React library but has since seen other applications. TypeScript 1.6 supports embedding, type checking, and optionally compiling JSX directly into JavaScript.

#### New `.tsx` file extension and `as` operator

TypeScript 1.6 introduces a new `.tsx` file extension.  This extension does two things: it enables JSX inside of TypeScript files, and it makes the new `as` operator the default way to cast (removing any ambiguity between JSX expressions and the TypeScript prefix cast operator). For example:

```ts
var x = <any> foo;
// is equivalent to:
var x = foo as any;
```

#### Using React

To use JSX-support with React you should use the [React typings](https://github.com/borisyankov/DefinitelyTyped/tree/master/react). These typings define the `JSX` namespace so that TypeScript can correctly check JSX expressions for React. For example:

```ts
/// <reference path="react.d.ts" />

interface Props {
  name: string;
}

class MyComponent extends React.Component<Props, {}> {
  render() {
    return <span>{this.props.foo}</span>
  }
}

<MyComponent name="bar" />; // OK
<MyComponent name={0} />; // error, `name` is not a number
```

#### Using other JSX framworks

JSX element names and properties are validated against the `JSX` namespace. Please see the [[JSX]] wiki page for defining the `JSX` namespace for your framework.

#### Output generation

TypeScript ships with two JSX modes: `preserve` and `react`.
- The `preserve` mode will keep JSX expressions as part of the output to be further consumed by another transform step. *Additionally the output will have a `.jsx` file extension.*
- The `react` mode will emit `React.createElement`, does not need to go through a JSX transformation before use, and the output will have a `.js` file extension.

See the [[JSX]] wiki page for more information on using JSX in TypeScript.

## Intersection types

TypeScript 1.6 introduces intersection types, the logical complement of union types. A union type `A | B` represents an entity that is either of type `A` or type `B`, whereas an intersection type `A & B` represents an entity that is both of type `A` *and* type `B`.

##### Example

```ts
function extend<T, U>(first: T, second: U): T & U {
    let result = <T & U> {};
    for (let id in first) {
        result[id] = first[id];
    }
    for (let id in second) {
        if (!result.hasOwnProperty(id)) {
            result[id] = second[id];
        }
    }
    return result;
}

var x = extend({ a: "hello" }, { b: 42 });
var s = x.a;
var n = x.b;
```

```ts
type LinkedList<T> = T & { next: LinkedList<T> };

interface Person {
    name: string;
}

var people: LinkedList<Person>;
var s = people.name;
var s = people.next.name;
var s = people.next.next.name;
var s = people.next.next.next.name;
```

```ts
interface A { a: string }
interface B { b: string }
interface C { c: string }

var abc: A & B & C;
abc.a = "hello";
abc.b = "hello";
abc.c = "hello";
```

See [issue #1256](https://github.com/Microsoft/TypeScript/issues/1256) for more information.

## Local type declarations

Local class, interface, enum, and type alias declarations can now appear inside function declarations. Local types are block scoped, similar to variables declared with `let` and `const`. For example:

```ts
function f() {
    if (true) {
        interface T { x: number }
        let v: T;
        v.x = 5;
    }
    else {
        interface T { x: string }
        let v: T;
        v.x = "hello";
    }
}
```

The inferred return type of a function may be a type declared locally within the function. It is not possible for callers of the function to reference such a local type, but it can of course be matched structurally. For example:

```ts
interface Point {
    x: number;
    y: number;
}

function getPointFactory(x: number, y: number) {
    class P {
        x = x;
        y = y;
    }
    return P;
}

var PointZero = getPointFactory(0, 0);
var PointOne = getPointFactory(1, 1);
var p1 = new PointZero();
var p2 = new PointZero();
var p3 = new PointOne();
```

Local types may reference enclosing type parameters and local class and interfaces may themselves be generic. For example:

```ts
function f3() {
    function f<X, Y>(x: X, y: Y) {
        class C {
            public x = x;
            public y = y;
        }
        return C;
    }
    let C = f(10, "hello");
    let v = new C();
    let x = v.x;  // number
    let y = v.y;  // string
}
```

## Class expressions

TypeScript 1.6 adds support for ES6 class expressions. In a class expression, the class name is optional and, if specified, is only in scope in the class expression itself. This is similar to the optional name of a function expression. It is not possible to refer to the class instance type of a class expression outside the class expression, but the type can of course be matched structurally. For example:

```ts
let Point = class {
    constructor(public x: number, public y: number) { }
    public length() {
        return Math.sqrt(this.x * this.x + this.y * this.y);
    }
};
var p = new Point(3, 4);  // p has anonymous class type
console.log(p.length());
```

## Extending expressions

TypeScript 1.6 adds support for classes extending arbitrary expression that computes a constructor function. This means that built-in types can now be extended in class declarations.

The `extends` clause of a class previously required a type reference to be specified. It now accepts an expression optionally followed by a type argument list. The type of the expression must be a constructor function type with at least one construct signature that has the same number of type parameters as the number of type arguments specified in the `extends` clause. The return type of the matching construct signature(s) is the base type from which the class instance type inherits. Effectively, this allows both real classes and "class-like" expressions to be specified in the `extends` clause.

Some examples:

```ts
// Extend built-in types

class MyArray extends Array<number> { }
class MyError extends Error { }

// Extend computed base class

class ThingA {
    getGreeting() { return "Hello from A"; }
}

class ThingB {
    getGreeting() { return "Hello from B"; }
}

interface Greeter {
    getGreeting(): string;
}

interface GreeterConstructor {
    new (): Greeter;
}

function getGreeterBase(): GreeterConstructor {
    return Math.random() >= 0.5 ? ThingA : ThingB;
}

class Test extends getGreeterBase() {
    sayHello() {
        console.log(this.getGreeting());
    }
}
```

## `abstract` classes and methods

TypeScript 1.6 adds support for `abstract` keyword for classes and their methods. An abstract class is allowed to have methods with no implementation, and cannot be constructed.

##### Examples

```ts
abstract class Base {
    abstract getThing(): string;
    getOtherThing() { return 'hello'; }
}

let x = new Base(); // Error, 'Base' is abstract

// Error, must either be 'abstract' or implement concrete 'getThing'
class Derived1 extends Base { }

class Derived2 extends Base {
    getThing() { return 'hello'; }
    foo() {
        super.getThing();// Error: cannot invoke abstract members through 'super'
    }
}

var x = new Derived2(); // OK
var y: Base = new Derived2(); // Also OK
y.getThing(); // OK
y.getOtherThing(); // OK
```

## Generic type aliases

With TypeScript 1.6, type aliases can be generic. For example:

```ts
type Lazy<T> = T | (() => T);

var s: Lazy<string>;
s = "eager";
s = () => "lazy";

interface Tuple<A, B> {
    a: A;
    b: B;
}

type Pair<T> = Tuple<T, T>;
```

## Stricter object literal assignment checks

TypeScript 1.6 enforces stricter object literal assignment checks for the purpose of catching excess or misspelled properties. Specifically, when a fresh object literal is assigned to a variable or passed as an argument for a non-empty target type, it is an error for the object literal to specify properties that don't exist in the target type.

##### Examples

```ts
var x: { foo: number };
x = { foo: 1, baz: 2 };  // Error, excess property `baz`

var y: { foo: number, bar?: number };
y = { foo: 1, baz: 2 };  // Error, excess or misspelled property `baz`
```

A type can include an index signature to explicitly indicate that excess properties are permitted:

```ts
var x: { foo: number, [x: string]: any };
x = { foo: 1, baz: 2 };  // Ok, `baz` matched by index signature
```

## ES6 generators

TypeScript 1.6 adds support for generators when targeting ES6.

A generator function can have a return type annotation, just like a function. The annotation represents the type of the generator returned by the function. Here is an example:

```ts
function *g(): Iterable<string> {
    for (var i = 0; i < 100; i++) {
        yield ""; // string is assignable to string
    }
    yield * otherStringGenerator(); // otherStringGenerator must be iterable and element type assignable to string
}
```

A generator function with no type annotation can have the type annotation inferred. So in the following case, the type will be inferred from the yield statements:

```ts
function *g() {
    for (var i = 0; i < 100; i++) {
        yield ""; // infer string
    }
    yield * otherStringGenerator(); // infer element type of otherStringGenerator
}
```

## Experimental support for `async` functions

TypeScript 1.6 introduces experimental support of `async` functions when targeting ES6. Async functions are expected to invoke an asynchronous operation and await its result without blocking normal execution of the program. This accomplished through the use of an ES6-compatible `Promise` implementation, and transposition of the function body into a compatible form to resume execution when the awaited asynchronous operation completes.


An *async function* is a function or method that has been prefixed with the `async` modifier. This modifier informs the compiler that function body transposition is required, and that the keyword `await` should be treated as a unary expression instead of an identifier. An *Async Function* must provide a return type annotation that points to a compatible `Promise` type. Return type inference can only be used if there is a globally defined, compatible `Promise` type.

##### Example

```ts
var p: Promise<number> = /* ... */;
async function fn(): Promise<number> {
  var i = await p; // suspend execution until 'p' is settled. 'i' has type "number"
  return 1 + i;
}

var a = async (): Promise<number> => 1 + await p; // suspends execution.
var a = async () => 1 + await p; // suspends execution. return type is inferred as "Promise<number>" when compiling with --target ES6
var fe = async function(): Promise<number> {
  var i = await p; // suspend execution until 'p' is settled. 'i' has type "number"
  return 1 + i;
}

class C {
  async m(): Promise<number> {
    var i = await p; // suspend execution until 'p' is settled. 'i' has type "number"
    return 1 + i;
  }

  async get p(): Promise<number> {
    var i = await p; // suspend execution until 'p' is settled. 'i' has type "number"
    return 1 + i;
  }
}
```

## Nightly builds

While not strictly a language change, nightly builds are now available by installing with the following command:

```Shell
npm install -g typescript@next
```

## Adjustments in module resolution logic

Starting from release 1.6 TypeScript compiler will use different set of rules to resolve module names when targeting 'commonjs'. These [rules](https://github.com/Microsoft/TypeScript/issues/2338) attempted to model module lookup procedure used by Node. This effectively mean that node modules can include information about its typings and TypeScript compiler will be able to find it. User however can override module resolution rules picked by the compiler by using `--moduleResolution` command line option. Possible values are:
- 'classic' - module resolution rules used by pre 1.6 TypeScript compiler
- 'node' - node-like module resolution

## Merging ambient class and interface declaration

The instance side of an ambient class declaration can be extended using an interface declaration The class constructor object is unmodified. For example:

```ts
declare class Foo {
    public x : number;
}

interface Foo {
    y : string;
}

function bar(foo : Foo)  {
    foo.x = 1; // OK, declared in the class Foo
    foo.y = "1"; // OK, declared in the interface Foo
}
```

## User-defined type guard functions

TypeScript 1.6 adds a new way to narrow a variable type inside an `if` block, in addition to `typeof` and `instanceof`. A user-defined type guard functions is one with a return type annotation of the form `x is T`, where `x` is a declared parameter in the signature, and `T` is any type. When a user-defined type guard function is invoked on a variable in an `if` block, the type of the variable will be narrowed to `T`.

##### Examples

```ts
function isCat(a: any): a is Cat {
  return a.name === 'kitty';
}

var x: Cat | Dog;
if(isCat(x)) {
  x.meow(); // OK, x is Cat in this block
}
```

## `exclude` property support in tsconfig.json

A tsconfig.json file that doesn't specify a files property (and therefore implicitly references all *.ts files in all subdirectories) can now contain an exclude property that specifies a list of files and/or directories to exclude from the compilation. The exclude property must be an array of strings that each specify a file or folder name relative to the location of the tsconfig.json file. For example:
```json
{
    "compilerOptions": {
        "out": "test.js"
    },
    "exclude": [
        "node_modules",
        "test.ts",
        "utils/t2.ts"
    ]
}
```
The `exclude` list does not support wilcards. It must simply be a list of files and/or directories.

## `--init` command line option

Run `tsc --init` in a directory to create an initial `tsconfig.json` in this directory with preset defaults. Optionally pass command line arguments along with `--init` to be stored in your initial tsconfig.json on creation.

# TypeScript 1.5

## ES6 Modules ##

TypeScript 1.5 supports ECMAScript 6 (ES6) modules. ES6 modules are effectively TypeScript external modules with a new syntax: ES6 modules are separately loaded source files that possibly import other modules and provide a number of externally accessible exports. ES6 modules feature several new export and import declarations. It is recommended that TypeScript libraries and applications be updated to use the new syntax, but this is not a requirement. The new ES6 module syntax coexists with TypeScript's original internal and external module constructs and the constructs can be mixed and matched at will.

#### Export Declarations

In addition to the existing TypeScript support for decorating declarations with `export`, module members can also be exported using separate export declarations, optionally specifying different names for exports using `as` clauses.

```ts
interface Stream { ... }
function writeToStream(stream: Stream, data: string) { ... }
export { Stream, writeToStream as write };  // writeToStream exported as write
```

Import declarations, as well, can optionally use `as` clauses to specify different local names for the imports. For example:

```ts
import { read, write, standardOutput as stdout } from "./inout";
var s = read(stdout);
write(stdout, s);
```

As an alternative to individual imports, a namespace import can be used to import an entire module:

```ts
import * as io from "./inout";
var s = io.read(io.standardOutput);
io.write(io.standardOutput, s);
```

#### Re-exporting

Using `from` clause a module can copy the exports of a given module to the current module without introducing local names.

```ts
export { read, write, standardOutput as stdout } from "./inout";
```

`export *` can be used to re-export all exports of another module. This is useful for creating modules that aggregate the exports of several other modules.

```ts
export function transform(s: string): string { ... }
export * from "./mod1";
export * from "./mod2";
```

#### Default Export

An export default declaration specifies an expression that becomes the default export of a module:

```ts
export default class Greeter {
    sayHello() {
        console.log("Greetings!");
    }
}
```

Which in turn can be imported using default imports:

```ts
import Greeter from "./greeter";
var g = new Greeter();
g.sayHello();
```


#### Bare Import

A "bare import" can be used to import a module only for its side-effects.

```ts
import "./polyfills";
```

For more information about module, please see the [ES6 module support spec](https://github.com/Microsoft/TypeScript/issues/2242).

## Destructuring in declarations and assignments

TypeScript 1.5 adds support to ES6 destructuring declarations and assignments.

#### Declarations

A destructuring declaration introduces one or more named variables and initializes them with values extracted from properties of an object or elements of an array.

For example, the following sample declares variables `x`, `y`, and `z`, and initializes them to `getSomeObject().x`, `getSomeObject().y` and `getSomeObject().z` respectively:

```ts
var { x, y, z} = getSomeObject();
```

Destructuring declarations also works for extracting values from arrays:

```ts
var [x, y, z = 10] = getSomeArray();
```

Similarly, destructuring  can be used in function parameter declarations:

```ts
function drawText({ text = "", location: [x, y] = [0, 0], bold = false }) {
    // Draw text
}

// Call drawText with an object literal
var item = { text: "someText", location: [1,2,3], style: "italics" };
drawText(item);
```

#### Assignments

Destructuring patterns can also be used in regular assignment expressions. For instance, swapping two variables can be written as a single destructuring assignment:

```ts
var x = 1;
var y = 2;
[x, y] = [y, x];
```

## `namespace` keyword

TypeScript used the `module` keyword to define both "internal modules" and "external modules"; this has been a bit of confusion for developers new to TypeScript. "Internal modules" are closer to what most people would call a namespace; likewise, "external modules" in JS speak really just are modules now.

> Note: Previous syntax defining internal modules are still supported.

**Before**:
```ts
module Math {
    export function add(x, y) { ... }
}
```

**After**:
```ts
namespace Math {
    export function add(x, y) { ... }
}
```

## `let` and `const` support
ES6 `let` and `const` declarations are now supported when targeting ES3 and ES5.

#### Const

```ts
const MAX = 100;

++MAX; // Error: The operand of an increment or decrement
       //        operator cannot be a constant.
```

#### Block scoped

```ts
if (true) {
  let a = 4;
  // use a
}
else {
  let a = "string";
  // use a
}

alert(a); // Error: a is not defined in this scope
```

## for..of support

TypeScript 1.5 adds support to ES6 for..of loops on arrays for ES3/ES5 as well as full support for Iterator interfaces when targetting ES6.

##### Example

The TypeScript compiler will transpile for..of arrays to idiomatic ES3/ES5 JavaScript when targeting those versions:

```ts
for (var v of expr) { }
```

will be emitted as:

```js
for (var _i = 0, _a = expr; _i < _a.length; _i++) {
    var v = _a[_i];
}
```

## Decorators
> TypeScript decorators are based on the [ES7 decorator proposal](https://github.com/wycats/javascript-decorators).

A decorator is:
- an expression
- that evaluates to a function
- that takes the target, name, and property descriptor as arguments
- and optionally returns a property descriptor to install on the target object

> For more information, please see the [Decorators](https://github.com/Microsoft/TypeScript/issues/2249) proposal.

##### Example

Decorators `readonly` and `enumerable(false)` will be applied to the property `method` before it is installed on class `C`. This allows the decorator to change the implementation, and in this case, augment the descriptor to be writable: false and enumerable: false.

```ts
class C {
  @readonly
  @enumerable(false)
  method() { }
}

function readonly(target, key, descriptor) {
    descriptor.writable = false;
}

function enumerable(value) {
  return function (target, key, descriptor) {
     descriptor.enumerable = value;
  }
}
```

## Computed properties
Initializing an object with dynamic properties can be a bit of a burden. Take the following example:

```ts
type NeighborMap = { [name: string]: Node };
type Node = { name: string; neighbors: NeighborMap;}

function makeNode(name: string, initialNeighbor: Node): Node {
    var neighbors: NeighborMap = {};
    neighbors[initialNeighbor.name] = initialNeighbor;
    return { name: name, neighbors: neighbors };
}
```

Here we need to create a variable to hold on to the neighbor-map so that we can initialize it. With TypeScript 1.5, we can let the compiler do the heavy lifting:

```ts
function makeNode(name: string, initialNeighbor: Node): Node {
    return {
        name: name,
        neighbors: {
            [initialNeighbor.name]: initialNeighbor
        }
    }
}
```

## Support for `UMD` and `System` module output

In addition to `AMD` and `CommonJS` module loaders, TypeScript now supports emitting modules `UMD` ([Universal Module Definition](https://github.com/umdjs/umd)) and [`System`](https://github.com/systemjs/systemjs) module formats.

**Usage**:
> tsc --module umd

and

> tsc --module system


## Unicode codepoint escapes in strings

ES6 introduces escapes that allow users to represent a Unicode codepoint using just a single escape.

As an example, consider the need to escape a string that contains the character '𠮷'.  In UTF-16/UCS2, '𠮷' is represented as a surrogate pair, meaning that it's encoded using a pair of 16-bit code units of values, specifically `0xD842` and `0xDFB7`. Previously this meant that you'd have to escape the codepoint as `"\uD842\uDFB7"`. This has the major downside that it’s difficult to discern two independent characters from a surrogate pair.

With ES6’s codepoint escapes, you can cleanly represent that exact character in strings and template strings with a single escape: `"\u{20bb7}"`. TypeScript will emit the string in ES3/ES5 as `"\uD842\uDFB7"`.

## Tagged template strings in ES3/ES5

In TypeScript 1.4, we added support for template strings for all targets, and tagged templates for just ES6. Thanks to some considerable work done by [@ivogabe](https://github.com/ivogabe), we bridged the gap for for tagged templates in ES3 and ES5.

When targeting ES3/ES5, the following code

```ts
function oddRawStrings(strs: TemplateStringsArray, n1, n2) {
    return strs.raw.filter((raw, index) => index % 2 === 1);
}

oddRawStrings `Hello \n${123} \t ${456}\n world`
```

will be emitted as

```js
function oddRawStrings(strs, n1, n2) {
    return strs.raw.filter(function (raw, index) {
        return index % 2 === 1;
    });
}
(_a = ["Hello \n", " \t ", "\n world"], _a.raw = ["Hello \\n", " \\t ", "\\n world"], oddRawStrings(_a, 123, 456));
var _a;
```

## AMD-dependency optional names

`/// <amd-dependency path="x" />` informs the compiler about a non-TS module dependency that needs to be injected in the resulting module's require call; however, there was no way to consume this module in the TS code.

The new `amd-dependency name` property allows passing an optional name for an amd-dependency:

```ts
/// <amd-dependency path="legacy/moduleA" name="moduleA"/>
declare var moduleA:MyType
moduleA.callStuff()
```

Generated JS code:

```js
define(["require", "exports", "legacy/moduleA"], function (require, exports, moduleA) {
    moduleA.callStuff()
});
```

## Project support through `tsconfig.json`

Adding a `tsconfig.json` file in a directory indicates that the directory is the root of a TypeScript project. The tsconfig.json file specifies the root files and the compiler options required to compile the project. A project is compiled in one of the following ways:

- By invoking tsc with no input files, in which case the compiler searches for the tsconfig.json file starting in the current directory and continuing up the parent directory chain.
- By invoking tsc with no input files and a -project (or just -p) command line option that specifies the path of a directory containing a tsconfig.json file.

##### Example

```json
{
    "compilerOptions": {
        "module": "commonjs",
        "noImplicitAny": true,
        "sourceMap": true,
    }
}
```

See the [tsconfig.json wiki page](https://github.com/Microsoft/TypeScript/wiki/tsconfig.json) for more details.

## `--rootDir` command line option

Option `--outDir` duplicates the input hierarchy in the output. The compiler computes the root of the input files as the longest common path of all input files; and then uses that to replicate all its substructure in the output.

Sometimes this is not desirable, for instance inputs `FolderA/FolderB/1.ts` and `FolderA/FolderB/2.ts` would result in output structure mirroring `FolderA/FolderB/`. Now if a new file `FolderA/3.ts` is added to the input, the output structure will pop out to mirror `FolderA/`.

`--rootDir` specifies the input directory to be mirrored in output instead of computing it.

## `--noEmitHelpers` command line option

The TypeSript compiler emits a few helpers like `__extends` when needed. The helpers are emitted in every file they are referenced in. If you want to consolidate all helpers in one place, or override the default behavior, use `--noEmitHelpers` to instructs the compiler not to emit them.


## `--newLine` command line option

By default the output new line character is `\r\n` on Windows based systems and `\n` on *nix based systems. `--newLine` command line flag allows overriding this behavior and specifying the new line character to be used in generated output files.

## `--inlineSourceMap` and `inlineSources` command line options

`--inlineSourceMap` causes source map files to be written inline in the generated `.js` files instead of in a independent `.js.map` file.  `--inlineSources` allows for additionally inlining the source `.ts` file into the

# TypeScript 1.4

## Union types

### Overview

Union types are a powerful way to express a value that can be one of several types. For example, you might have an API for running a program that takes a commandline as either a `string`, a `string[]` or a function that returns a `string`. You can now write:

```ts
interface RunOptions {
   program: string;
   commandline: string[]|string|(() => string);
}
```

Assignment to union types works very intuitively -- anything you could assign to one of the union type's members is assignable to the union:

```ts
var opts: RunOptions = /* ... */;
opts.commandline = '-hello world'; // OK
opts.commandline = ['-hello', 'world']; // OK
opts.commandline = [42]; // Error, number is not string or string[]
```

When reading from a union type, you can see any properties that are shared by them:

```ts
if (opts.length === 0) { // OK, string and string[] both have 'length' property
  console.log("it's empty");
}
```

Using Type Guards, you can easily work with a variable of a union type:

```ts
function formatCommandline(c: string|string[]) {
    if (typeof c === 'string') {
        return c.trim();
    }
    else {
        return c.join(' ');
    }
}
```

### Stricter Generics

With union types able to represent a wide range of type scenarios, we've decided to improve the strictness of certain generic calls. Previously, code like this would (surprisingly) compile without error:

```ts
function equal<T>(lhs: T, rhs: T): boolean {
  return lhs === rhs;
}

// Previously: No error
// New behavior: Error, no best common type between 'string' and 'number'
var e = equal(42, 'hello');
```
With union types, you can now specify the desired behavior at both the function declaration site and the call site:

```ts
// 'choose' function where types must match
function choose1<T>(a: T, b: T): T { return Math.random() > 0.5 ? a : b }
var a = choose1('hello', 42); // Error
var b = choose1<string|number>('hello', 42); // OK

// 'choose' function where types need not match
function choose2<T, U>(a: T, b: U): T|U { return Math.random() > 0.5 ? a : b }
var c = choose2('bar', 'foo'); // OK, c: string
var d = choose2('hello', 42); // OK, d: string|number
```

### Better Type Inference

Union types also allow for better type inference in arrays and other places where you might have multiple kinds of values in a collection:

```ts
var x = [1, 'hello']; // x: Array<string|number>
x[0] = 'world'; // OK
x[0] = false; // Error, boolean is not string or number
```

## `let` declarations

In JavaScript, `var` declarations are "hoisted" to the top of their enclosing scope. This can result in confusing bugs:

```ts
console.log(x); // meant to write 'y' here
/* later in the same block */
var x = 'hello';
```

The new ES6 keyword `let`, now supported in TypeScript, declares a variable with more intuitive "block" semantics. A `let` variable can only be referred to after its declaration, and is scoped to the syntactic block where it is defined:

```ts
if (foo) {
    console.log(x); // Error, cannot refer to x before its declaration
    let x = 'hello';
}
else {
    console.log(x); // Error, x is not declared in this block
}
```

`let` is only available when targeting ECMAScript 6 (`--target ES6`).

## `const` declarations
The other new ES6 declaration type supported in TypeScript is `const`. A `const` variable may not be assigned to, and must be initialized where it is declared. This is useful for declarations where you don't want to change the value after its initialization:

```ts
const halfPi = Math.PI / 2;
halfPi = 2; // Error, can't assign to a `const`
```

`const` is only available when targeting ECMAScript 6 (`--target ES6`).

## Template strings

TypeScript now supports ES6 template strings. These are an easy way to embed arbitrary expressions in strings:

```ts
var name = "TypeScript";
var greeting  = `Hello, ${name}! Your name has ${name.length} characters`;
```

When compiling to pre-ES6 targets, the string is decomposed:

```js
var name = "TypeScript!";
var greeting = "Hello, " + name + "! Your name has " + name.length + " characters";
```

## Type Guards

A common pattern in JavaScript is to use `typeof` or `instanceof` to examine the type of an expression at runtime. TypeScript now understands these conditions and will change type inference accordingly when used in an `if` block.

Using `typeof` to test a variable:

```ts
var x: any = /* ... */;
if(typeof x === 'string') {
    console.log(x.subtr(1)); // Error, 'subtr' does not exist on 'string'
}
// x is still any here
x.unknown(); // OK
```

Using `typeof` with union types and `else`:

```ts
var x: string | HTMLElement = /* ... */;
if(typeof x === 'string') {
    // x is string here, as shown above
}
else {
    // x is HTMLElement here
    console.log(x.innerHTML);
}
```

Using `instanceof` with classes and union types:

```ts
class Dog { woof() { } }
class Cat { meow() { } }
var pet: Dog|Cat = /* ... */;
if (pet instanceof Dog) {
    pet.woof(); // OK
}
else {
    pet.woof(); // Error
}
```

## Type Aliases

You can now define an *alias* for a type using the `type` keyword:

```ts
type PrimitiveArray = Array<string|number|boolean>;
type MyNumber = number;
type NgScope = ng.IScope;
type Callback = () => void;
```

Type aliases are exactly the same as their original types; they are simply alternative names.

## `const enum` (completely inlined enums)
Enums are very useful, but some programs don't actually need the generated code and would benefit from simply inlining all instances of enum members with their numeric equivalents. The new `const enum` declaration works just like a regular `enum` for type safety, but erases completely at compile time.

```ts
const enum Suit { Clubs, Diamonds, Hearts, Spades }
var d = Suit.Diamonds;
```

Compiles to exactly:

```js
var d = 1;
```

TypeScript will also now compute enum values when possible:

```ts
enum MyFlags {
  None = 0,
  Neat = 1,
  Cool = 2,
  Awesome = 4,
  Best = Neat | Cool | Awesome
}
var b = MyFlags.Best; // emits var b = 7;
```

## `-noEmitOnError` commandline option

The default behavior for the TypeScript compiler is to still emit .js files if there were type errors (for example, an attempt to assign a `string` to a `number`). This can be undesirable on build servers or other scenarios where only output from a "clean" build is desired. The new flag `noEmitOnError` prevents the compiler from emitting .js code if there were any errors.

This is now the default for MSBuild projects; this allows MSBuild incremental build to work as expected, as outputs are only generated on clean builds.

## AMD Module names

By default AMD modules are generated anonymous. This can lead to problems when other tools are used to process the resulting modules like a bundlers (e.g. `r.js`).

The new `amd-module name` tag allows passing an optional module name to the compiler:

```ts
//// [amdModule.ts]
///<amd-module name='NamedModule'/>
export class C {
}
```
Will result in assigning the name `NamedModule` to the module as part of calling the AMD `define`:

```js
//// [amdModule.js]
define("NamedModule", ["require", "exports"], function (require, exports) {
    var C = (function () {
        function C() {
        }
        return C;
    })();
    exports.C = C;
});
```

# TypeScript 1.3

## Protected

The new `protected` modifier in classes works like it does in familiar languages like C++, C#, and Java. A `protected` member of a class is visible only inside subclasses of the class in which it is declared:

```ts
class Thing {
  protected doSomething() { /* ... */ }
}

class MyThing extends Thing {
  public myMethod() {
    // OK, can access protected member from subclass
    this.doSomething();
  }
}
var t = new MyThing();
t.doSomething(); // Error, cannot call protected member from outside class
```

## Tuple types

Tuple types express an array where the type of certain elements is known, but need not be the same. For example, you may want to represent an array with a `string` at position 0 and a `number` at position 1:

```ts
// Declare a tuple type
var x: [string, number];
// Initialize it
x = ['hello', 10]; // OK
// Initialize it incorrectly
x = [10, 'hello']; // Error
```

When accessing an element with a known index, the correct type is retrieved:

```ts
console.log(x[0].substr(1)); // OK
console.log(x[1].substr(1)); // Error, 'number' does not have 'substr'
```

Note that in TypeScript 1.4, when accessing an element outside the set of known indices, a union type is used instead:

```ts
x[3] = 'world'; // OK
console.log(x[5].toString()); // OK, 'string' and 'number' both have toString
x[6] = true; // Error, boolean isn't number or string
```

# TypeScript 1.1

## Performance Improvements

The 1.1 compiler is typically around 4x faster than any previous release. See [this blog post for some impressive charts.](http://blogs.msdn.com/b/typescript/archive/2014/10/06/announcing-typescript-1-1-ctp.aspx)

## Better Module Visibility Rules

TypeScript now only strictly enforces the visibility of types in modules if the `--declaration` flag is provided. This is very useful for Angular scenarios, for example:

```ts
module MyControllers {
  interface ZooScope extends ng.IScope {
    animals: Animal[];
  }
  export class ZooController {
    // Used to be an error (cannot expose ZooScope), but now is only
    // an error when trying to generate .d.ts files
    constructor(public $scope: ZooScope) { }
    /* more code */
  }
}
```
