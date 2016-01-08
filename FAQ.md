# Glossary and Terms in this FAQ

### Dogs, Cats, and Animals, Oh My
For many code examples, we'll use a hypothetical type hierarchy:
```ts
       Animal
      /      \
    Dog      Cat
```
That is, all `Dog`s are `Animal`s, all `Cat`s are `Animal`s, but e.g. a function expecting a `Dog` cannot accept an argument of type `Cat`. If you want to try these examples in the TypeScript Playground, start with this template:
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
Many answers relating to the type system make reference to [Substitutability](https://en.wikipedia.org/wiki/Liskov_substitution_principle). This is a principle that says if an object `X` can be used in place of some object `Y`, then `X` is a *subtype* of `Y`. We also commonly say that `X` is *assignable to* `Y` (these terms have slightly different meanings in TypeScript, but the difference is not important here).

In other words, if I ask for a `fork`, a `spork` is an acceptable *substitute* because it has the same functions and properties of a `fork` (three prongs and a handle).

# FAQs

## Type System Behavior

### Why are getters without setters not considered read-only?

I wrote some code like this and expected an error:
```ts
class Foo {
   get bar() {
     return 42;
   }
}
let x = new Foo();
// Expected error here
x.bar = 10;
```

A getter without a setter *does not* create a read-only property.
See [Issue #12](https://github.com/Microsoft/TypeScript/issues/12) for the suggestion tracking this issue.


### Why are function parameters bivariant?

 > I wrote some code like this and expected an error:
 > ```ts
 > function trainDog(d: Dog) { ... }
 > function cloneAnimal(source: Animal, done: (result: Animal) => void): void { ... }
 > let c = new Cat();
 > // Runtime error here occurs because we end up invoking 'trainDog' with a 'Cat'
 > cloneAnimal(c, trainDog);
 > ```

This is a unsoundness resulting from the lack of explicit covariant / contravariant annotations in the type system.

To see why this happens, consider two questions: Is `Dog[]` a subtype of `Animal[]` ? *Should* `Dog[]` be a subtype of `Animal[]`?

The second question (*should* `Dog[]` be a subtype of `Animal[]`?) is an easier one.
What if the answer was "no" ?
```ts
function checkAnimalsAreAwake(arr: Animal[]) { ... }

let myPets: Dog[] = [spot, fido];
// Error? Can't substitute Dog[] for Animal[] ?
checkAnimalsAreAwake(myPets);
```
This would be *incredibly annoying*.
The code here is 100% correct.
There's not a good reason to reject this program on the basis that `Dog[]` can't be used in place of `Animal[]` -- clearly a group of `Dog`s is a group of `Animal`s.

Back to the first question.
When the type system is *deciding* whether or not `Dog[]` is a subtype of `Animal[]`, it does the following computation (written here as if the compiler took no optimizations), among many others:

 * Is `Dog[]` assignable to Animal[]` ?
  * Is each member of `Dog[]` assignable to `Animal[]` ?
    * Is `Dog[].push` assignable to `Animal[].push` ?
      * Is the type `(x: Dog) => number` assignable to `(x: Animal) => number` ?
        * Is the first parameter type in `(x: Dog) => number` assignable to first parameter type in `(x: Animal) => number`?
          * Is `Dog` assignable to `Animal`?
            * Yes

TODO: Keep writing


### Why are functions with fewer parameters assignable to functions that take more parameters?

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
First, refer to the "substitutability" primer at the top of the FAQ -- `handler` is a valid argument for `callback` because it can safely ignored extra parameters.

Second, let's consider another case:
```ts
let items = [1, 2, 3];
items.forEach(arg => console.log(arg));
```
This is isomorphic to the example that "wanted" an error.
At runtime, `forEach` invokes the given callback with three arguments (value, index, array), but most of the time the callback only uses one or two of the arguments.
This is a very common JavaScript pattern and it would be burdensome to have to explicitly declare unused parameters.

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
> // Expected an error because 'doSomething' returns number, but 'callMeMaybe' expects void-returning function
> callMeMaybe(doSomething);
> ```

This is the expected and desired behavior.
First, refer to the "substitutability" primer at the top of the FAQ -- the fact that `doSomething` returns "more" information than `callMeMaybe` is a valid substitution.

Second, let's consider another case:
```ts
let items = [1, 2];
callMeMaybe(() => items.push(3));
```
This is isomorphic to the example that "wanted" an error.
`Array#push` returns a number (the new length of the array), but it's a safe substitute to use for a `void`-returning function.

Another way to think of this is that a `void`-returning callback type says "I'm not going to look at your return value, if one exists".

### Why are all types are assignable to empty interfaces?

> I wrote some code like this an expected an error:
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

### Why is `A<string>` assignable to `A<number>` for `interface A<T> { }`?

### Can I make a type alias nominal?

### How do I prevent two types from being structurally compatible?

### How do I check at runtime if an object implements some interface?

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
A popular method is to check for properties an on object.
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
The intent of `<T>x` is to say "TypeScript, please treat `x` as a `T`", not to perform a typesafe runtime conversion.
Because types are erased, there is no direct equivalent of C#'s `expr as` type or `(type)expr` syntax.

-------------------------------------------------------------------------------------
## Functions

## Why can't I use `x` in the destructuring `function f({ x: number }) { /* ... */ }`?
> I wrote some code like this and got an unexpected error:
> ```ts
> function f({x: number}) {
>   // Error, x is not defined?
>   console.log(x);
> }
> ```

Destructuring syntax is counterintuitive for those accustomed to looking at TypeScript type literals.
The syntax `f({x: number})` declares a destructuring *from the property* `x` *to the local* `number`.

Looking at the emitted code for this is instructive:
```ts
function f(_a) {
    // Not really what we were going for
    var number = _a.x;
}
```

To write this code correctly, you should write:
```ts
function f({x}: {x: number}) {
  // OK
  console.log(x);
}
```

If you can provide a default for all properties, it's preferable to write:
```ts
function f({x = 0}) {
  // x: number
  console.log(x);
}
```

-------------------------------------------------------------------------------------
## Classes

### Why aren't classes nominal?


### Why does `this` get orphaned on my instance methods?


### What's the difference between `Bar` and `typeof Bar` when `Bar` is a `class` ?


### What's the difference between `declare class` and `inteface`?

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

### Why am I getting "TypeError: [base class name] is not defined in `__extends` ?
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
Add a script tag for the base class's output *before* the script tag for the derived class.

## Why am I getting "TypeError: Cannot read property 'prototype' of undefined" in `__extends` ?
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

-------------------------------------------------------------------------------------
## Modules

### Why are imports being elided in my emit?

### Why is my output file empty when I use module exports with `--outFile`?

### Why don't namespaces merge across different module files?

-------------------------------------------------------------------------------------

## Enums

### Why are enums nominal?

### What's the difference between `enum` and `const enum`s?

TODO: Write up common symptoms of `enum` / `const enum` confusion.

See http://stackoverflow.com/questions/28818849/how-do-the-different-enum-variants-work-in-typescript


-------------------------------------------------------------------------------------

## Decorators

### Decorators on function declarations

-------------------------------------------------------------------------------------

## JSX and React

### I wrote `declare var MyComponent: React.Component;`, why can't I write `<MyComponent />`




## Why am I getting an error about a missing index signature?


### Why don't I get type checking for `(number) => string` or `(T) => T`?

I wrote some code like this and expected an error:
```ts
let myFunc: (number) => string = (n) => 'The number in hex is ' + n.toString(16);
// Expected error because boolean is not number
console.log(myFunc(true));
```

Parameter names in function types are required.
The code as written describes a function taking one parameter named `number` of type `any`.
In other words, this declaration
```ts
let myFunc: (number) => string
```
is equivalent to this one
```ts
let myFunc: (number: any) => string
```

To avoid this problem, turn on the `noImplicitAny` flag, which will issue a warning about the implicit `any` parameter type.


## External Tools

### How do I write unit tests with TypeScript?


### How do I control file ordering in combined output (`--out`) ?

### Why do my derived class property initializers overwrite values set in the base class constructor?
See #1617 for this and other initialization order questions

### What does the error "Exported variable [name] has or is using private name [name]" mean?

Port in content from #6307