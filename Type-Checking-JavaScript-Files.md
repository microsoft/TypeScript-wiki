TypeScript 2.3 and later support type-checking and reporting errors in `.js` files with `--checkJs`.

You can skip checking some files by adding `// @ts-nocheck` comment to them; conversely, you can choose to check only a few `.js` files by adding a `// @ts-check` comment to them without setting `--checkJs`.
You can also ignore errors on specific lines by adding `// @ts-ignore` on the preceding line.

Here are some notable differences on how checking work in `.js` file from `.ts` file:

## Using types in JSDoc

In a `.js` file, types can often be inferred just like in `.ts` files.
Likewise, when types can't be inferred, they can be specified using JSDoc the same way that type annotations do in a `.ts` file.
Just like Typescript, `--noImplicitAny` will give you errors on the places that the compiler could not infer a type.
(With the exception of open-ended object literals; see below for details.)

JSDoc annotations adorning a declaration will be used to set the type of that declaration. For example:

```js
/** @type {number} */
var x;

x = 0;      // OK
x = false;  // Error: boolean is not assignable to number
```

You can find the full list of supported JSDoc patterns in the [JSDoc support in JavaScript documentation](https://github.com/Microsoft/TypeScript/wiki/JSDoc-support-in-JavaScript).


## Properties are inferred from assignments in class bodies

ES2015 does not have a means for declaring properties on classes. Properties are dynamically assigned, just like object literals.

In a `.js` file, the compiler infers properties from property assignments inside the class body.
The type of properties is the type given in the constructor, unless it's not defined there, or the type in the constructor is undefined or null.
In that case, the type is the union of the types of all the right-hand values in these assignments.
Properties defined in the constructor are always assumed to exist, whereas ones defined just in methods, getters, or setters are considered optional.

```js
class C {
    constructor() {
        this.constructorOnly = 0
        this.constructorUnknown = undefined
    }
    method() {
        this.constructorOnly = false // error, constructorOnly is a number
        this.constructorUnknown = "plunkbat" // ok, constructorUnknown is string | undefined
        this.methodOnly = 'ok'  // ok, but y could also be undefined
    }
    method2() {
        this.methodOnly = true  // also, ok, y's type is string | boolean | undefined
    }
}
```

If the property type can't be inferred, annotate the assignment in the constructor with JSDoc to specify the type.
You don't even have to give a value if it will be initialised later:

```js
class C {
    constructor() {
        /** @type {number | undefined} */
        this.prop = undefined;
        /** @type {number | undefined} */
        this.count;
    }
}


let c = new C();
c.prop = 0;          // OK
c.count = "string";  // Error: string is not assignable to number|undefined
```

If properties are never set in the class body, they are considered unknown. If your class has properties that are only read from, consider adding an initialization in the constructor to undefined, e.g. `this.prop = undefined;`.

## Constructor functions work basically the same as classes

Before ES2015, Javascript used constructor functions to model classes.
The compiler supports this pattern and understands constructor functions as equivalent to ES2015 classes.
The property inferences rules described above work exactly the same way.

```js
function C {
    this.constructorOnly = 0
    this.constructorUnknown = undefined
}
C.prototype.method = function() {
    this.constructorOnly = false // error
    this.constructorUnknown = "plunkbat" // OK, the type is string | undefined
}
```

## CommonJS module input support

In a `.js` file, Typescript understands the CommonJS module format is allowed as an input module format. Assignments to `exports`, and `module.exports` are recognized as export declarations. Similarly, `require` function calls are recognized as module imports. For example:

```js
// same as `import module "fs"`
const fs = require("fs");


// same as `export function readFile`
module.exports.readFile = function(f) {
    return fs.readFileSync(f);
}
```

The module support in Javascript is much more syntactically forgiving than Typescript's module support.
Most combinations of assignments and declarations are supported.

TODO: Enumerate the kinds of modules and things

## Object literals are open-ended

By default object literals in variable declarations provide the type of a declaration. No new members can be added that were not specified in the original initialization. This rule is relaxed in a `.js` file; object literals have an open-ended type, allowing adding and looking up properties that were not defined originally. For instance:

```js
var obj = { a: 1 };
obj.b = 2;  // Allowed
```

Object literals get a default index signature `[x:string]: any` that allows them to be treated as open maps instead of closed objects.

Similar to other special JS checking behaviors, this behavior can be changed by specifying a JSDoc type for the variable. For example:

```js
/** @type {{a: number}} */
var obj = { a: 1 };
obj.b = 2;  // Error, type {a: number} does not have property b
```

## null/undefined/[] initializers are of type any or any[]

Any variable, parameter or property that is initialized with null or undefined will have type any, even if strict null checks is turned on.
Any variable, parameter or property that is initialized with [] will have type any[], even if strict null checks is turned on.
The only exception is for properties that have multiple initializers as described above.

```js
function Foo(i = null) {
    if (!i) i = 1;
    var j = undefined;
    j = 2;
    this.l = [];
}
var foo = new Foo();
foo.l.push(foo.i);
foo.l.push("end");
```

## Many things are namespaces now

Constructor functions are namespaces:

```js
function Outer() {
  this.y = 2
}
Outer.Inner = class {
}
```

TODO: Other kinds of initializers (IIFEs, {}, classes, functions)
TODO: Other variant patterns
TODO: Nested nested example


## Function parameters are optional by default

Since there is no way to specify optionality on parameters in JS (without specifying a default value), all function parameters in `.js` file are considered optional. Calls with fewer arguments are allowed.

It is important to note that it is an error to call a function with too many arguments.

For instance:

```js
function bar(a, b){
    console.log(a + " " + b);
}

bar(1);       // OK, second argument considered optional
bar(1, 2);
bar(1, 2, 3); // Error, too many arguments
```

JSDoc annotated functions are excluded from this rule. Use JSDoc optional parameter syntax to express optionality. e.g.:

```js
/**
 * @param {string} [somebody] - Somebody's name.
 */
function sayHello(somebody) {
    if (!somebody) {
        somebody = 'John Doe';
    }
    alert('Hello ' + somebody);
}

sayHello();
```

## Var-args parameter declaration inferred from use of `arguments`

A function whose body has a reference to the `arguments` reference is implicitly considered to have a var-arg parameter (i.e. `(...arg: any[]) => any`). Use JSDoc var-arg syntax to specify the type of the arguments.

TODO: Give an example of the correct jsdoc

## Unspecified type parameters default to `any`

An unspecified generic type parameter defaults to `any`. There are few places where this happens:

#### In extends clause:

For instance, `React.Component` is defined to have two generic type parameters, `Props` and `State`.
In a `.js` file, there is no legal way to specify these in the extends clause. By default the type arguments will be `any`:

```js
import { Component } from "react";

class MyComponent extends Component {
    render() {
        this.props.b; // Allowed, since this.props is of type any
    }
}
```

Use JSDoc `@augments` to specify the types explicitly. for instance:

```js
import { Component } from "react";

/**
 * @augments {Component<{a: number}, State>}
 */
class MyComponent extends Component {
    render() {
        this.props.b; // Error: b does not exist on {a:number}
    }
}
```

#### In JSDoc references

An unspecified generic type argument in JSDoc defaults to any:

```js
/** @type{Array} */
var x = [];

x.push(1);        // OK
x.push("string"); // OK, x is of type Array<any>


/** @type{Array.<number>} */
var y = [];

y.push(1);        // OK
y.push("string"); // Error, string is not assignable to number

```

#### In function calls

A call to generic functions uses arguments to infer the generic type parameters. Sometimes this process fails to infer any types, mainly because of lack on inference sources; in these cases, the generic type parameters will default to `any`. For example:

```js
var p = new Promise((resolve, reject) => { reject() });

p; // Promise<any>;
```
