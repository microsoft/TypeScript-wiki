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

## Why are imports being elided in my emit?

## Why are function parameters bivariant?

 > I wrote some code like this and expected an error
 > ```ts
 > function trainDog(d: Dog) { ... }
 > function cloneAnimal(source: Animal, done: (result: Animal) => void): void { ... }
 > let c = new Cat();
 > // Runtime error here occurs because we end up invoking 'trainDog' with a 'Cat'
 > cloneAnimal(c, trainDog);
 > ```

This is a unsoundness resulting from the lack of explicit covariant / contravariant annotations in the type system.

To see why this happens, consider two questions: Is `Dog[]` a subtype of `Animal[]` ? *Should* `Dog[]` be a subtype of `Animal[]`?

The second question (*should* `Dog[]` be a subtype of `Animal[]`?) is an easier one. What if the answer was "no" ?
```ts
function checkAnimalsAreAwake(arr: Animal[]) { ... }

let myPets: Dog[] = [spot, fido];
// Error? Can't substitute Dog[] for Animal[] ?
checkAnimalsAreAwake(myPets);
```
This would be *incredibly annoying*. The code here is 100% correct. There's not a good reason to reject this program on the basis that `Dog[]` can't be used in place of `Animal[]` -- clearly a group of `Dog`s is a group of `Animal`s.

Back to the first question. When the type system is *deciding* whether or not `Dog[]` is a subtype of `Animal[]`, it does the following computation (written here as if the compiler took no optimizations), among many others:

 * Is `Dog[]` assignable to Animal[]` ?
  * Is each member of `Dog[]` assignable to `Animal[]` ?
    * Is `Dog[].push` assignable to `Animal[].push` ?
      * Is the type `(x: Dog) => number` assignable to `(x: Animal) => number` ?
        * Is the first parameter type in `(x: Dog) => number` assignable to first parameter type in `(x: Animal) => number`?
          * Is `Dog` assignable to `Animal`?
            * Yes

TODO: Keep writing


## Why are functions with fewer parameters assignable to functions that take more parameters?

## Why are functions returning non-`void` assignable to function returning `void`?

## Why are all types are assignable to empty interfaces?

## Why is `A<string>` assignable to `A<number>` for `interface A<T> { }`?

## Why aren't classes nominal?

## Why are enums nominal?

## Why does `this` get orphaned on my instance methods?

## Why is my output file empty when I use module exports with `--outFile`?

## Decorators on function declarations

## Why are getters without setters not considered read-only?

See [Issue #12](https://github.com/Microsoft/TypeScript/issues/12)

## I wrote `declare var MyComponent: React.Component;`, why can't I write `<MyComponent />`

## Why don't namespaces merge across different module files?

## What's the difference between `enum` and `const enum`s?

## What's the difference between `declare class` and `inteface`?

## Can I make a type alias nominal?

## How do I prevent two types from being structurally compatible?

## What does it mean for an interface to extend a class?

## Why am I getting an error about a missing index signature?

## Why can't I use `x` in `function f({ x: number }) { /* ... */ }`?

## Why don't I get type checking for `(number) => string`?

## How do I check at runtime if an object implements some interface?

## Why doesn't this incorrect cast throw a runtime error?

## How do I write unit tests with TypeScript?

## Why am I getting "TypeError: Cannot read property 'prototype' of undefined" in `__extends` ?
(Derived class put before base class)

## Why do my derived class property initializers overwrite values set in the base class constructor?
See #1617 for this and other initialization order questions

## What does the error "Exported variable [name] has or is using private name [name]" mean?

Port in content from #6307