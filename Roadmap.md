- [X] Feature already available in [`typescript@next`](https://github.com/Microsoft/TypeScript-Handbook/blob/master/pages/Nightly%20Builds.md)


# Future

* Support [type-checking of computed properties for constants and Symbols](https://github.com/Microsoft/TypeScript/issues/5579)
* [Support for project references](https://github.com/Microsoft/TypeScript/issues/3469)
* [Variadic types](https://github.com/Microsoft/TypeScript/issues/5453)
* Investigate [nominal typing support](https://github.com/Microsoft/TypeScript/issues/202)
* [Flattening declarations](https://github.com/Microsoft/TypeScript/issues/4433)
* Implement new ES Decorator proposal
* Investigate [Ambient](https://github.com/Microsoft/TypeScript/issues/2900), [Deprecated](https://github.com/Microsoft/TypeScript/issues/390), and [Conditional](https://github.com/Microsoft/TypeScript/issues/3538) decorators
* Decorators for function expressions/arrow functions
* Refactoring support in Language Service API
* Investigate [Language Service extensibility](https://github.com/Microsoft/TypeScript/issues/6508)

# 2.2 (February 2017)

* [ ] [Support for `new.target`](https://github.com/Microsoft/TypeScript/issues/2551)
* [ ] [Generator support for ES3/ES5](https://github.com/Microsoft/TypeScript/issues/1564)
* [ ] [Asynchronous iterators](https://github.com/Microsoft/TypeScript/issues/11326)
* [X] [Support for JSX spread children](https://github.com/Microsoft/TypeScript/issues/9495)
* [ ] [Better error reporting for errors in `tsconfig.json`](https://github.com/Microsoft/TypeScript/pull/12336)
* [ ] [Generic defaults](https://github.com/Microsoft/TypeScript/issues/2175)
* [ ] [JSX stateless components overload resolution](https://github.com/Microsoft/TypeScript/issues/9703)
* [ ] [Better support for literal types in JSX attributes](https://github.com/Microsoft/TypeScript/issues/10393)
* [X] More Quick fixes: [Add missing imports](https://github.com/Microsoft/TypeScript/pull/11768), [Implement interface](https://github.com/Microsoft/TypeScript/pull/11547), and [Remove unused declarations](https://github.com/Microsoft/TypeScript/pull/11546)

# 2.1 (December 2016)

* [X] Switch to a [transformation-based emitter](https://github.com/Microsoft/TypeScript/issues/5595)
* [X] [`async`/`await` support for ES5/ES3](https://github.com/Microsoft/TypeScript/pull/9175)
* [X] Support for [external helpers library](https://github.com/Microsoft/TypeScript/issues/3364)
* [X] [Static types for dynamically named properties (`keyof T` and `T[K]`)](https://github.com/Microsoft/TypeScript/pull/11929)
* [X] [Mapped types (e.g. `{ [P in K]: T[P] }`)](https://github.com/Microsoft/TypeScript/pull/12114)
* [X] Support [ESNext object property spread and rest](https://github.com/Microsoft/TypeScript/issues/2103)
* [X] [Better inference for literal types](https://github.com/Microsoft/TypeScript/pull/10676)
* [X] [Use returned values from super calls as 'this'](https://github.com/Microsoft/TypeScript/pull/10762)
* [X] [Control flow analysis for implicit any variables](https://github.com/Microsoft/TypeScript/pull/11263)
* [X] [Control flow analysis for array construction](https://github.com/Microsoft/TypeScript/pull/11432)
* [X] [Narrow string and number types in literal equality checks](https://github.com/Microsoft/TypeScript/pull/11587)
* [X] [Contextual typing of partially annotated signatures](https://github.com/Microsoft/TypeScript/pull/11673)
* [X] [Normalize union/intersection type combinations](https://github.com/Microsoft/TypeScript/pull/11717)
* [X] New [`--jsxFactory`](https://github.com/Microsoft/TypeScript/pull/12135)
* [X] New [`--alwaysStrict`](https://github.com/Microsoft/TypeScript/issues/10758)
* [X] Support for [`--target ES2016`, `--target ES2017`](https://github.com/Microsoft/TypeScript/pull/11407) and `--target ESNext`
* [X] [Configuration inheritance](https://github.com/Microsoft/TypeScript/issues/9876)
* [X] [Go to implementation support](https://github.com/Microsoft/TypeScript/pull/10482)
* [X] [Completions in imports and triple-slash reference paths](https://github.com/Microsoft/TypeScript/issues/188)
* [X] [Quick fixes support](https://github.com/Microsoft/TypeScript/issues/6943) in language service API
* [X] [Untyped](https://github.com/Microsoft/TypeScript/pull/11889) (implicit-any) imports

# 2.0

* [X] [Non-nullable types](https://github.com/Microsoft/TypeScript/pull/7140)
* [X] [Control flow based type analysis](https://github.com/Microsoft/TypeScript/pull/8010)
* [X] [Discriminated union types](https://github.com/Microsoft/TypeScript/pull/9163)
* [X] [Improved definition file acquisition](https://github.com/Microsoft/TypeScript/issues/9184)
* [X] [Specifying `this` types for functions](https://github.com/Microsoft/TypeScript/issues/3694)
* [X] [Type guards on property access](https://github.com/Microsoft/TypeScript/issues/186)
* [X] [Readonly properties and index signatures](https://github.com/Microsoft/TypeScript/pull/6532)
* [X] [Use path mappings in module resolution](https://github.com/Microsoft/TypeScript/issues/5039)
* [X] [Shorthand ambient module declarations and wildcard matching in module names](https://github.com/Microsoft/TypeScript/issues/6615)
* [X] [Implicit index signatures](https://github.com/Microsoft/TypeScript/pull/7029)
* [X] Support [private and protected constructors](https://github.com/Microsoft/TypeScript/pull/6885)
* [X] Support [`abstract` properties](https://github.com/Microsoft/TypeScript/issues/4669)
* [X] [Optional properties in classes](https://github.com/Microsoft/TypeScript/pull/8625)
* [X] [The `never` type](https://github.com/Microsoft/TypeScript/pull/8652)
* [X] [`--skipLibCheck` compiler option](https://github.com/Microsoft/TypeScript/pull/8735)
* [X] Support for [declaration output folder using `--declarationDir`](https://github.com/Microsoft/TypeScript/issues/6723)
* [X] [Glob support in tsconfig.json](https://github.com/Microsoft/TypeScript/issues/1927)
* [X] [Improve lib.d.ts modularity](https://github.com/Microsoft/TypeScript/issues/494) and new [`--lib`](https://github.com/Microsoft/TypeScript/issues/6974) support
* [X] Support for [UMD module definitions](https://github.com/Microsoft/TypeScript/issues/7125)
* [X] [Trailing Commas in Function Param Lists](https://github.com/Microsoft/TypeScript/issues/7279)
* [X] Support for [jsdoc `@typedef`](https://github.com/Microsoft/TypeScript/pull/8103) for JS files
* [X] [Completion lists for string literals](https://github.com/Microsoft/TypeScript/issues/606)
* [X] [Module name in imports allow .js extension](https://github.com/Microsoft/TypeScript/issues/4595)
* [X] [Support 'target:es5' with 'module:es6' ](https://github.com/Microsoft/TypeScript/issues/6319)
* [X] Flag unused declarations with [`--noUnusedLocals` and `--noUnusedParameters`](https://github.com/Microsoft/TypeScript/pull/9200)
* [X] [Number, boolean, and Enum literal types](https://github.com/Microsoft/TypeScript/pull/9407)

# 1.8

* [X] [Allow JavaScript in TypeScript compilations with `--allowjs`](https://github.com/Microsoft/TypeScript/issues/4792)
* [X] [Allow captured `let`/`const` in loops](https://github.com/Microsoft/TypeScript/issues/3915)
* [X] [Flag unreachable code](https://github.com/Microsoft/TypeScript/pull/4788)
* [X] [Concatenate module output with `--outFile`](https://github.com/Microsoft/TypeScript/pull/5090)
* [X] [Accept comments in tsconfig.json](https://github.com/Microsoft/TypeScript/issues/4987)
* [X] [Stylize error messages in terminal output with `--pretty`](https://github.com/Microsoft/TypeScript/pull/5140)
* [X] [Support for `--outFile` with named pipes, sockets, and special devices](https://github.com/Microsoft/TypeScript/issues/4841)
* [X] [Support computed property with literal names](https://github.com/Microsoft/TypeScript/issues/4653)
* [X] [String literal types](https://github.com/Microsoft/TypeScript/pull/5185)
* [X] [Stateless Functional Components in JSX](https://github.com/Microsoft/TypeScript/issues/5478)
* [X] [Improved union/intersection type inference](https://github.com/Microsoft/TypeScript/pull/5738)
* [X] [Support for F-Bounded Polymorphism](https://github.com/Microsoft/TypeScript/pull/5949)
* [X] [Support full path for `-project`/`-p` parameter](https://github.com/Microsoft/TypeScript/issues/2869)
* [X] [Extract type information from JSDoc in js files](https://github.com/Microsoft/TypeScript/issues/4790)
* [X] [Support for `default` import interop with SystemJS using `--allowSyntheticDefaultImports`](https://github.com/Microsoft/TypeScript/issues/5285)
* [X] [Recognize prototype assignments in JavaScript files](https://github.com/Microsoft/TypeScript/pull/5876)
* [X] [Augmenting global/module scope from other modules](https://github.com/Microsoft/TypeScript/issues/4166)
* [X] [Use tsconfig.json as higher priority source of configuration in Visual Studio](https://github.com/Microsoft/TypeScript/issues/5287)
* [X] [`this`-based type guards](https://github.com/Microsoft/TypeScript/pull/5906)
* [X] Support for [custom JSX factories using `--reactNamespace`](https://github.com/Microsoft/TypeScript/pull/6146)
* [X] [Improved checking of for-in statements](https://github.com/Microsoft/TypeScript/pull/6379)
* [X] [Colorization of JSX code in VS 2015](https://github.com/Microsoft/TypeScript/issues/4835)
* [X] Publish official [TypeScript NuGet packages](https://github.com/Microsoft/TypeScript/issues/3940)

# 1.7

* [X] [ES7 exponentiation operator](https://github.com/Microsoft/TypeScript/issues/4812)
* [X] [Polymorphic `this` type](https://github.com/Microsoft/TypeScript/pull/4910)
* [X] [Support `--module` with `--target es6`](https://github.com/Microsoft/TypeScript/issues/4806)
* [X] [Support for decorators when targeting ES3](https://github.com/Microsoft/TypeScript/pull/4741)
* [X] [`async`/`await` support for ES6 (Node v4)](https://github.com/Microsoft/TypeScript/pull/5231)
* [X] [Improved checking of destructuring with literal initializers](https://github.com/Microsoft/TypeScript/pull/4598)

# 1.6

* [X] [ES6 Generators](https://github.com/Microsoft/TypeScript/issues/2873)
* [X] [Local types](https://github.com/Microsoft/TypeScript/pull/3266)
* [X] [Generic type aliases](https://github.com/Microsoft/TypeScript/issues/1616)
* [X] [Expressions in class extends clauses](https://github.com/Microsoft/TypeScript/pull/3516)
* [X] [Class expressions](https://github.com/Microsoft/TypeScript/issues/497)
* [X] [`exclude` property in tsconfig.json](https://github.com/Microsoft/TypeScript/pull/3188)
* [X] [User defined type guard functions](https://github.com/Microsoft/TypeScript/issues/1007)
* [X] [External module resolution enhancements](https://github.com/Microsoft/TypeScript/issues/2338)
* [X] [JSX support](https://github.com/Microsoft/TypeScript/pull/3564)
* [X] [Intersection types](https://github.com/Microsoft/TypeScript/pull/3622)
* [X] [`abstract` classes and methods](https://github.com/Microsoft/TypeScript/issues/3578)
* [X] [Strict object literal assignment checking](https://github.com/Microsoft/TypeScript/pull/3823)
* [X] [Declaration merging for classes and interfaces](https://github.com/Microsoft/TypeScript/pull/3333)
* [X] New [--init](https://github.com/Microsoft/TypeScript/issues/3079)

# 1.5

* [X] Support for [Destructuring](https://github.com/Microsoft/TypeScript/pull/1346)
* [X] Support for [Spread Operator](https://github.com/Microsoft/TypeScript/pull/1931)
* [X] Support for [ES6 Modules](https://github.com/Microsoft/TypeScript/issues/2242)
* [X] Support for [for..of](https://github.com/Microsoft/TypeScript/pull/2207)
* [X] Support for [ES6 Unicode specification](https://github.com/Microsoft/TypeScript/pull/2169)
* [X] Support for [Symbols](https://github.com/Microsoft/TypeScript/pull/1978)
* [X] Support for [Computed properties](https://github.com/Microsoft/TypeScript/issues/1082)
* [X] Support for [tsconfig.json files](https://github.com/Microsoft/TypeScript/pull/1692)
* [X] Support for [let and const in ES3/ES5](https://github.com/Microsoft/TypeScript/pull/2161)
* [X] Support for [tagged templates in ES3/ES5](https://github.com/Microsoft/TypeScript/pull/1589)
* [X] Expose a new editor interface through [TS Server](https://github.com/Microsoft/TypeScript/pull/2041)
* [X] Support for [ES7 Decorators proposal](https://github.com/Microsoft/TypeScript/issues/2249)
* [X] Support for [Decorator type metadata](https://github.com/Microsoft/TypeScript/pull/2589)
* [X] New [--rootDir](https://github.com/Microsoft/TypeScript/pull/2772)
* [X] New [ts.transpile API](https://github.com/Microsoft/TypeScript/issues/2499)
* [X] Support [--module umd](https://github.com/Microsoft/TypeScript/issues/2036)
* [X] Support [--module system](https://github.com/Microsoft/TypeScript/issues/2616)
* [X] New [--noEmitHelpers](https://github.com/Microsoft/TypeScript/pull/2901)
* [X] New [--inlineSourceMap](https://github.com/Microsoft/TypeScript/pull/2484)
* [X] New [--inlineSources](https://github.com/Microsoft/TypeScript/pull/2484)
* [X] New [--newLine](https://github.com/Microsoft/TypeScript/pull/2921)
* [X] New [--isolatedModules](https://github.com/Microsoft/TypeScript/issues/2499)
* [X] Support for new [`namespace` keyword](https://github.com/Microsoft/TypeScript/issues/2159)
* [X] Support for [tsconfig.json in Visual Studio 2015](https://github.com/Microsoft/TypeScript/issues/3124)
* [X] Improved [template literal highlighting in Visual Studio 2013](https://github.com/Microsoft/TypeScript/pull/2026)

# 1.4

* [X] Support for [Union Types and Type Guards](https://github.com/Microsoft/TypeScript/pull/824)
* [X] New [--noEmitOnError](https://github.com/Microsoft/TypeScript/pull/966)
* [X] New [--target ES6](https://github.com/Microsoft/TypeScript/commit/873c1df74b7c7dcba59eaccc1bb4bd4b0da18a35)
* [X] Support for [Let and Const](https://github.com/Microsoft/TypeScript/pull/904)
* [X] Support for [Template Literals](https://github.com/Microsoft/TypeScript/pull/960)
* [X] Library typings for ES6
* [X] Support for [Const enums](https://github.com/Microsoft/TypeScript/issues/1029)
* [X] Export Language Service public API

# 1.3

* [X] Language service re-write to target new compiler
* [X] Support for [protected members](https://github.com/Microsoft/TypeScript/pull/688) in classes
* [X] Support for [Tuple Types](https://github.com/Microsoft/TypeScript/pull/428)
