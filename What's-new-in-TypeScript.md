# TypeScript 1.5

## Destructuring in declarations and assignments

**Declarations**

A destructuring declaration introduces one or more named variables and initializes them with values extracted from properties of an object or elements of an array.

For example, the following sample declares a variables `x`, `y`, and `z`, and initializes them to `getSomeObject().x`, `getSomeObject().y` and `getSomeObject().q` respectively:

```ts
var { x, p: y, q: z = false } = getSomeObject();
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
```
declares a function drawText that takes a single parameter of the type

```ts
{ text?: string; location?: [number, number]; bold?: boolean; }
```

**Assignments**

Destructuring patterns can also be used in regular assignment expressions. For instance, swapping two variablescan be written as a single destructuring assignment:
```ts
var x = 1;  
var y = 2;  
[x, y] = [y, x];
```

## `let` and `const` support
ES6 `let` and `const` declarations are now supported when targeting ES3 and ES5. 

**Const**
```ts
const MAX = 100;

++MAX; // Error: The operand of an increment or decrement 
       //        operator cannot be a constant.
```

**Block scoped**

```ts
let x = 0;
{
   let x = "hello!";
   console.log(x); // hello!
}
console.log(x); // 0
```

## for..of support

TypeScript 1.5 adds support to for..of loops on arrays for ES3/ES5 as well as full support for Iterators for ES6.

**Example:**

The TypeScript compiler will transpile for..of arrays to idiomatic ES3/ES5 javascript:

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
TypeScript 1.5 comes along with ES7 decorators support. Decorators are functions that can alter or augment a class declaration.

As per the [ES7 proposal](https://github.com/wycats/javascript-decorators), A decorator is:
- an expression
- that evaluates to a function
- that takes the target, name, and property descriptor as arguments
- and optionally returns a property descriptor to install on the target object

> For more information, please see the [Decorators](https://github.com/Microsoft/TypeScript/issues/2249) proposal.

**Example:**

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

```TypeScript
type NeighborMap = { [name: string]: Node };
interface Node {
    name: string;
    neighbors: NeighborMap;
}

function makeNode(name: string, initialNeighbor: Node): Node {
    var neighbors: NeighborMap = {};
    neighbors[initialNeighbor.name] = initialNeighbor;
    return { name: name, neighbors: neighbors };
}
```

Here we need to create a variable to hold on to the neighbor-map so that we can initialize it. With TypeScript 1.5, we can let the compiler do the heavy lifting:

```TypeScript
function makeNode(name: string, initialNeighbor: Node): Node {
    return {
        name: name,
        neighbors: {
            [initialNeighbor.name] = initialNeighbor
        }
    }
}
```

## Unicode codepoint escapes in strings

ECMAScript 6 introduces escapes that allow users to represent a Unicode codepoint using just a single escape.

As an example, consider the need to escape a string that contains the character '𠮷'.  In UTF-16/UCS2, '𠮷' is represented as a surrogate pair, meaning that it's encoded using a pair of 16-bit code units of values, specifically `0xD842` and `0xDFB7`. Previously this meant that you'd have to escape the codepoint as `"\uD842\uDFB7"`. This has the major downside that it’s difficult to discern two independent characters from a surrogate pair.

With ES6’s codepoint escapes, you can cleanly represent that exact character in strings and template strings with a single escape: `"\u{20bb7}"`. TypeScript will emit the string in ES3/ES5 as `"\uD842\uDFB7"`.

## Tagged template strings in ES3/ES5

In TypeScript 1.4, we added support for template strings for all targets, and tagged templates for just ES6. Thanks to some considerable work done by [@ivogabe](https://github.com/ivogabe), we bridged the gap for for tagged templates in ES3 and ES5.

When targeting ES3/ES5, the following code

```TypeScript
function oddRawStrings(strs: TemplateStringsArray, n1, n2) {
    return strs.raw.filter((raw, index) => index % 2 === 1);
}

oddRawStrings `Hello \n${123} \t ${456}\n world`
```

will be emitted as

```JavaScript
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

```Typescript
/// <amd-dependency path="legacy/moduleA" name="moduleA"/>
declare var moduleA:MyType
moduleA.callStuff()
```
Generated JS code:
```
define(["require", "exports", "legacy/moduleA"], function (require, exports, moduleA) {
    moduleA.callStuff()
});
```

## Project support through `tsconfig.json`

Adding a `tsconfig.json` file in a directory indicates that the directory is the root of a TypeScript project. The tsconfig.json file specifies the root files and the compiler options required to compile the project. A project is compiled in one of the following ways:

- By invoking tsc with no input files, in which case the compiler searches for the tsconfig.json file starting in the current directory and continuing up the parent directory chain.
- By invoking tsc with no input files and a -project (or just -p) command line option that specifies the path of a directory containing a tsconfig.json file.

**Example:**
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
if(opts.length === 0) { // OK, string and string[] both have 'length' property
  console.log("it's empty");
}
```

Using Type Guards, you can easily work with a variable of a union type:
```ts
function formatCommandline(c: string|string[]) {
    if(typeof c === 'string') {
        return c.trim();
    } else {
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
if(foo) {
    console.log(x); // Error, cannot refer to x before its declaration
    let x = 'hello';
} else {
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
var x: string|HTMLElement = /* ... */;
if(typeof x === 'string') {
    // x is string here, as shown above
} else {
    // x is HTMLElement here
    console.log(x.innerHTML);
}
```

Using `instanceof` with classes and union types:
```ts
class Dog { woof() { } }
class Cat { meow() { } }
var pet: Dog|Cat = /* ... */;
if(pet instanceof Dog) {
    pet.woof(); // OK
} else {
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
By default AMD modules are generated anonymous. This can lead to problems when other tools are used to process the resulting modules like a bundlers (e.g. r.js). 

The new `amd-module name` tag allows passing an optional module name to the compiler:

```TypeScript
//// [amdModule.ts]
///<amd-module name='NamedModule'/>
export class C {
}
```
Will result in assigning the name `NamedModule` to the module as part of calling the AMD `define`:

```JavaScript
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