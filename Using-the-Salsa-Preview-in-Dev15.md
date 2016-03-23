# JavaScript "Salsa" Language Service

With the release of "Dev15 Preview", we have included a preview of a new JavaScript
language service, internally codenamed "Salsa". This may be switched on or off via a
registry key.

Salsa also powers the JavaScript language service in VS Code, and most of the below
info applies there also. See [VSCode release notes](https://github.com/Microsoft/vscode-docs/blob/vnext/release-notes/latest.md#languages---javascript)
for more info.

## Goals
In the last few releases of Visual Studio, the JavaScript language service has been
provided via an "execution based" model, which runs the code as you write it, and
examines the execuction environment when it reaches the current editing location to
provide information such as completion lists, signature help, etc.. This provided a
number of challenges, for example:

 - Visual Studio specific: The existing language service is tied to Visual Studio
 and Windows, meaning it cannot be shared across editors & platforms (such as VS Code).
 - Costly engineering: The bulk of the existing language service logic is written
 in C++, making iterating quickly and adding new features slower than desired.
 - Inconsistent experience: Being execution based, changes in code in one location
 can impact the intellisense experience in other locations in non-obvious ways.
 Timeouts are also used to halt execution if paths take too long, which can cause
 different behavior on different hardware.
 - No TypeScript integration: The current language service is entirely distinct from
 the TypeScript language service, meaning there is no shared context or integration
 across file types. Ideally TypeScript and JavaScript files should be able to
 contribute to each other's editing experience, and support similar JavaScript
 language features.
 
By basing the new JavaScript language service on the same codebase that powers the
TypeScript language service, the following benefits are realized:

 - The language service can be used outside of Visual Studio (as has been done by
 the VS Code team).
 - The team can focus on **one** casebase written in TypeScript to provide parsing,
 checking, intellisense, etc., resulting in reduced cost, faster iteration, and
 a common experience across the languages.
 - JavaScript and TypeScript files can be integrated within a project, including
 being aware of constructs across languages, and being emitted to downlevel code.
 (Features that are outlined below). 
 
## Overview
To enable the new "Salsa" language service experience in Visual Studio "15" Preview,
save the below as a local file named `salsa.reg` and open it to update the registry.
(Set the same registry key value to 0 to disable at any point).

```
Windows Registry Editor Version 5.00

[HKEY_CURRENT_USER\SOFTWARE\Microsoft\VisualStudio\15.0\TypeScriptLanguageService]
"UseTypeScriptExperimental"=dword:00000001
```

Now when you open a JavaScript file in Visual Studio, you should notice that
the editing experience is similar to that of TypeScript; namely richer types
flowing through constructs (as shown in the `Array.map` example below), or support
for more recent language features (as shown in the destructuring example below).

_**Richer intellisense**_

<img src="https://raw.githubusercontent.com/wiki/Microsoft/TypeScript/images/array.map.png" height="132" width="933"/>

_**Latest language features**_

<img src="https://raw.githubusercontent.com/wiki/Microsoft/TypeScript/images/destructuring.png" height="156" width="500"/>

## Features

### Intellisense based on type inference
The Salsa language service mostly uses the same inference as TypeScript to determine
the type of a value. For a variable or property, this is typically the type
of the value used to initialize it. For a function, the return type is infered
from the return statements, whereas the parameters are not infered (but may
be specified, as will be outlined later).

One common area where this can be limiting is in *expando* objects. These are
objects that have properties added after initialization. For example:

```javascript
var x = {a: true};
x.b = false;
x. // <- "x" is shown as only having the property "a" that it was initialized with
```

The type of `x` can be specified explicitly to give it the desired type (as
outlined later).

For JavaScript files, some additional inference is done, specifically "es3-style"
classes, and CommonJS-style module patterns are recognized. For example:

```javascript
function Foo(param1) {
    this.prop = param1;
}
Foo.prototype.getIt = function () { return this.prop; };
// Foo will appear as a class, and instances will have a 'prop' property and a 'getIt' method.

exports.Foo = Foo;
// This file will appear as an external module with a 'Foo' export.
// Note that assigning a value to "module.exports" is also supported.
```

### Intellisense based on JsDoc annotations
Where type inference does not provide the desired type information, (or just for
documentation purposes), type information may be provided explicitly via JsDoc
annotations. For example, to give the variable `x` the desired type in the example
above, it may be written as:

```javascript
/**
 * @type {{a: boolean, b: boolean, c: number}}
 */
var x = {a: true};
x.b = false;
x. // <- "x" is shown as having properties a, b, and c of the types specified
```

As mentioned, function parameters are also never infered. Thus if it is desired
that they have a specific type, then JsDoc may be used for this purpose also:

```javascript
/**
 * @param {string} param1 - The first argument to this function
 */
function Foo(param1) {
    this.prop = param1; // "param1" (and thus "this.prop") are now of type "string".
}
```
 
See [this doc](TODO) for the JsDoc annotations currently supported.


### Intellisense based on TypeScript definitions
With JavaScript and TypeScript now being based on the same language service, they
are able to interact in a richer way. For example, JavaScript intellisense can be
provided for values declared in a `.d.ts` file. Types such as interface and classes
may also be declared in TypeScript, and those types are available for use in JsDoc
comments. 

Below shows a simple example of a TypeScript definition file providing such type
information (via an interface) to a JavaScript file in the same project (via a
JsDoc tag).

_**TypeScript declarations used in JavaScript**_

<img src="https://raw.githubusercontent.com/wiki/Microsoft/TypeScript/images/decl1.png" height="264" width="912"/>

### Automatic acquisition of type definitions
In the TypeScript world, the most popular JavaScript libraries have their APIs
described by `.d.ts` files, and the most common repository for such definitions
is on [DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped).

With the Salsa language service enabled, it will by default try to detect which
JavaScript libraries are in use, and automatically download and reference the
corresponding `.d.ts` file that describes the library in order to provide rich
intellisense. The files are downloaded to a cache located under the user folder
at `%LOCALAPPDATA%\Microsoft\TypeScript`.

Currently auto-detection works for dependencies downloaded from NPM (via reading
the `package.json` file) or Bower (via reading the `bower.json` file).

Note: This feature currently only works in Salsa running within Visual Studio "15",
and is not available in VS Code yet.

### Compiling JavaScript down-level
One of the key features TypeScript provides is the ability to use the latest JavaScript
language features, and emit code that can execute in JavaScript runtimes that don't 
yet understand those newer features. With JavaScript using the same language service,
it too can now take advantage of this same feature.

Before this can be set up, some understanding of the configuration options is
required. TypeScript is configured via a `tsconfig.json` file. In the absense of such
a file, some default values are used. For compatibility reasons, these defaults are
different in a context where only JavaScript files (and optionally `.d.ts` files)
are present. To compile JavaScript files, a `tsconfig.json` file must be added,
and some of these defaults must then be set explicitly. The main settings of interest
are outlined below:

 - `allowJs`: This value must be set to `true` for JavaScript files to be recognised.
By default this is `false`, as TypeScript compiles to JavaScript, and this is to avoid
the compiler including files in just emitted.
 - `outDir`: This should be set to a location not included in the project, in order
that the emitted JavaScript files are not detected and included in the project.
 - `module`: If using modules, this settings tells the compiler which module format
the emitted code should use (e.g. `commonjs` for Node or bundlers such as Browserify).
 - `exclude`: This setting states which folders not to include in the project. The
output location, as well as non-project folders such as `node_modules` or `temp`, should
be added to this setting.
 - `enableAutoDiscovery`: This setting enables the automatic detection and download of
definition files as outlined above.
 - `compileOnSave`: This settings tells the compiler if it should recompile any time
a source file is saved in Visual Studio.

In order to convert JavaScript files to CommonJS modules in an `./out` folder, settings
similar to the below might be included in a `tsconfig.json` file.

```javascript
{
  "compilerOptions": {
    "module": "commonjs",
    "allowJs": true,
    "outDir": "out"
  },
  "exclude": [
    "node_modules",
    "wwwroot",
    "out"
  ],
  "compileOnSave": true,
  "typingOptions": {
    "enableAutoDiscovery": true
  }
}
```

With the above settings in place, if a source file (`./app.js`) existed which
contains several ECMAScript 2015 language features as shown below:

```javascript
import {Subscription} from 'rxjs/Subscription';

class Foo {
    sayHi(name) {
        return `Hi ${name}, welcome to Salsa!`;
    }
}

export let sqr = x => x * x;
export default Subscription;
```

Then a file would be emitted to `./out/app.js` targetting ECMAScript 5 (the default)
that looks something like the below:

```javascript
"use strict";
var Subscription_1 = require('rxjs/Subscription');
var Foo = (function () {
    function Foo() {
    }
    Foo.prototype.sayHi = function (name) {
        return "Hi " + name + ", welcome to Salsa!";
    };
    return Foo;
}());
exports.sqr = function (x) { return x * x; };
Object.defineProperty(exports, "__esModule", { value: true });
exports.default = Subscription_1.Subscription;
//# sourceMappingURL=app.js.map
```

  
### Mixing JavaScript and TypeScript source
With the above configuration file in place, TypeScript and JavaScript files may
be used in the same compilation. For example, existing JavaScript code using the
CommonJS module format, may be imported and consumed by TypeScript code using the
ECMAScript 2015 module syntax. Conversely, TypeScript code written to provide a 
well-defined API contract for a service, may be referenced by JavaScript code 
that is written to call that service, thus providing rich intellisense at design time.

### JSX and React support
TODO: Example

## Known issues
TODO:
 - MSBuild skipping if no .ts files
 - Need to set `allowJs` for mixed mode
 - Need to set `outDir` to avoid errors compiling .js files
 
 