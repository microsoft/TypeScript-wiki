# TypeScript 1.8 (upcoming)

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

## Improved checking for `for..in` statements

Previously the type of a `for..in` variable is inferred to `any`; that allowed the compiler to ignore invalid uses within the `for..in` body.

Starting with TS 1.8,:
* The type of a variable declared in a `for..in` statement is implicitly `string`.
* When an object with a numeric index signature of type `T` (such as an array) is indexed by a `for..in` variable of a containing `for..in` statement for an object *with* a numeric index signature and *without* a string index signature (again such as an array), the value produced is of type `T`.

##### Example

```typescript
var a: MyObject[];
for (var x in a) {   // Type of x is implicitly string
    var obj = a[x];  // Type of obj is MyObject
}
```

## `this`-based type guards

TODO

## Official TypeScript NuGet package

Starting with TypeScript 1.8, official NuGet packages are available for the Typescript Compiler (tsc.exe) as well as the MSBuild integration (Microsoft.TypeScript.targets and Microsoft.TypeScript.Tasks.dll).

Stable packages are available here:
* [Microsoft.TypeScript.Compiler](https://www.nuget.org/packages/Microsoft.TypeScript.Compiler/)
* [Microsoft.TypeScript.MSBuild](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/)

Also, a nightly NuGet packages to match the [nightly npm package](http://blogs.msdn.com/b/typescript/archive/2015/07/27/introducing-typescript-nightlies.aspx) is available on www.myget.org:

* [TypeScript-Preview](https://www.myget.org/gallery/typescript-preview)

## Prettier error messages from `tsc`

We understand that a ton of monochrome output can be a little difficult on the eyes.
Colors can help discern where a message starts and ends, and these visual clues are important when error output gets overwhelming.

By just passing the `--pretty` command line option, TypeScript gives more colorful output with context about where things are going wrong.

![Showing off pretty error messages in ConEmu](https://raw.githubusercontent.com/wiki/Microsoft/TypeScript/images/new-in-typescript/pretty01.png)

## Colorization of JSX code in VS 2015 

JSX tags now have their own classifications in Visual Studio 2015 with TypeScript 1.8. 

![jsx](https://cloud.githubusercontent.com/assets/8052307/12271404/b875c502-b90f-11e5-93d8-c6740be354d1.png)

The classification can be further customized by changing the font and color settings for the `VB XML` color and font settings through Tools\Options\Environment\Fonts and Colors page.

## The `--project` (`-p`) flag can now take any file path

The `--project` command line option originally could only take paths to a folder containing a `tsconfig.json`. Given the different scenarios for build configurations, it made sense to allow `--project` to point to any other compatible JSON file. For instance, a user might want to target ES2015 with CommonJS modules for Node 5, but ES5 with AMD modules for the browser. With this new work, users can easily manage two separate build targets using `tsc` alone without having to perform hacky workarounds like placing `tsconfig.json` files in separate directories.

The old behavior still remains the same if given a directory - the compiler will try to find a file in the directory named `tsconfig.json`.

## Allow comments in tsconfig.json

It is always nice to be able to document your configuration! tsconfig.json now accepts single and multi-line comments. 

```ts
{
    "compilerOptions": {
        "target": "ES2015", // running on node v5, yaay!
        "sourceMap": true // makes debugging easier
    },
    /* Excluded
      * Files
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


# TypeScript 1.7

## `async`/`await` support in ES6 targets (Node v4+)

TypeScript now supports asynchronous functions for engines that have native support for ES6 generators, e.g. Node v4 and above.
Asynchronous functions are prefixed with the `async` keyword; `await` suspends the execution until an asynchronous function return promise is fulfilled and unwraps the value from the `Promise` returned.

##### Example

In the following example, each input element will be printed out one at a time with a 400ms delay:

```TypeScript
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

#### Example

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

```TypeScript
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

```TypeScript
import calc from "./BasicCalculator";

let v = new calc(2)
    .multiply(5)
    .add(1)
    .currentValue();
``` 

This often opens up very elegant ways of writing code; however, there was a problem for classes that wanted to extend from `BasicCalculator`.
Imagine a user wanted to start writing a `ScientificCalculator`:

```TypeScript
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

```TypeScript
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

```TypeScript
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

```typescript
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

```typescript
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

```typescript
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

```typescript
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

```typescript
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

```typescript
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

```typescript
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

```typescript
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

#### Examples

```TypeScript
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

```typescript
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

TypeScript 1.6 enforces stricter object literal assignment checks for the purpose of catching excess or misspelled properties. Specifically, when a fresh object literal is assigned to a variable or passed for a parameter of a non-empty target type, it is an error for the object literal to specify properties that don't exist in the target type.

#### Examples

```typescript
var x: { foo: number };
x = { foo: 1, baz: 2 };  // Error, excess property `baz`

var y: { foo: number, bar?: number };
y = { foo: 1, baz: 2 };  // Error, excess or misspelled property `baz`
```

A type can include an index signature to explicitly indicate that excess properties are permitted:

```typescript
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

#### Example

```TypeScript
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

#### Examples

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

Which in tern can be imported using default imports:

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
```TypeScript
module Math {
    export function add(x, y) { ... }
}
```

**After**:
```TypeScript
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

#### Example:

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
> TypeScript decorator is based on the [ES7 decorator proposal](https://github.com/wycats/javascript-decorators). 

A decorator is:
- an expression
- that evaluates to a function
- that takes the target, name, and property descriptor as arguments
- and optionally returns a property descriptor to install on the target object

> For more information, please see the [Decorators](https://github.com/Microsoft/TypeScript/issues/2249) proposal.

#### Example:

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
type Node = { name: string; neighbors: NeighborMap;}

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

#### Example:
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

Sometimes this is not desirable, for instance inputs `FolderA\FolderB\1.ts` and `FolderA\FolderB\2.ts` would result in output structure mirroring `FolderA\FolderB\`. now if a new file `FolderA\3.ts` is added to the input, the output structure will pop out to mirror `FolderA\`.

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