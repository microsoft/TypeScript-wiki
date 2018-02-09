- [X] Feature already available in [`typescript@next`](https://github.com/Microsoft/TypeScript-Handbook/blob/master/pages/Nightly%20Builds.md)

# Future

* [Support for project references](https://github.com/Microsoft/TypeScript/issues/3469)
* [Variadic types](https://github.com/Microsoft/TypeScript/issues/5453)
* Investigate [nominal typing support](https://github.com/Microsoft/TypeScript/issues/202)
* [Flattening declarations](https://github.com/Microsoft/TypeScript/issues/4433)
* Implement new ES Decorator proposal
* Investigate [Ambient](https://github.com/Microsoft/TypeScript/issues/2900), [Deprecated](https://github.com/Microsoft/TypeScript/issues/390), and [Conditional](https://github.com/Microsoft/TypeScript/issues/3538) decorators
* Decorators for function expressions/arrow functions
* [Investigate a way to reference types within a module without import declarations](https://github.com/Microsoft/TypeScript/issues/14844)

# 2.8 (March 2018)
* [X] [Conditional types](https://github.com/Microsoft/TypeScript/pull/21316)
* [X] [Type inference in conditional types](https://github.com/Microsoft/TypeScript/pull/21496)
* [ ] [Predefined conditional types](https://github.com/Microsoft/TypeScript/pull/21847)
* [X] New [`--emitDeclarationOnly`](https://github.com/Microsoft/TypeScript/pull/20735)
* [ ] [Better handling for namespace-patterns in .js files](https://github.com/Microsoft/TypeScript/issues/7632)

# 2.7 (January 2018)

* [X] [Support for properties named with with `const`-declared symbols](https://github.com/Microsoft/TypeScript/pull/15473)
* [X] [Strict property initialization checks in classes](https://github.com/Microsoft/TypeScript/pull/20075)
* [X] [Definite assignment assertions](https://github.com/Microsoft/TypeScript/pull/20166)
* [X] [Improved type inference for object literals](https://github.com/Microsoft/TypeScript/pull/19513)
* [X] [Improved handling of structurally identical classes](https://github.com/Microsoft/TypeScript/pull/19671)
* [X] [Type guards inferred from `in` operator](https://github.com/Microsoft/TypeScript/issues/10485)
* [X] [Numeric separators](https://github.com/Microsoft/TypeScript/pull/20324)
* [X] [Support for `import d from "cjs"` form CommonJS modules with `--esModuleInterop`](https://github.com/Microsoft/TypeScript/pull/19675)
* [X] [Apply all quick fixes in a file](https://github.com/Microsoft/TypeScript/pull/20338)
* [X] [Refactors to convert CommonJS module to ES6 module](https://github.com/Microsoft/TypeScript/pull/19916)
* [X] [Support for incremental builder compiler API](https://github.com/Microsoft/TypeScript/pull/20234)
* [X] [Quick fix to add missing `async` keyword](https://github.com/Microsoft/TypeScript/pull/21069)
* [X] [Completion list preselects suggested item based on context](https://github.com/Microsoft/TypeScript/pull/20020)
* [X] Completion list includes [`this.`](https://github.com/Microsoft/TypeScript/pull/21231), [brackets](https://github.com/Microsoft/TypeScript/pull/20547), and [curlies for JSX](https://github.com/Microsoft/TypeScript/pull/21372)

# 2.6 (October 2017)

* [X] [Strict function types](https://github.com/Microsoft/TypeScript/pull/18654)
* [x] [Support for JSX Fragment](https://github.com/Microsoft/TypeScript/pull/19249)
* [X] [Cached tagged template objects in modules](https://github.com/Microsoft/TypeScript/pull/18300)
* [X] [Include localized diagnostics in npm](https://github.com/Microsoft/TypeScript/pull/18702)
* [X] [Suppress errors in .ts files using `// @ts-ignore` comments](https://github.com/Microsoft/TypeScript/pull/18457)
* [x] [Speed improvements to `--watch`](https://github.com/Microsoft/TypeScript/issues/10879)
* [X] [Automatic imports from completion lists](https://github.com/Microsoft/TypeScript/issues/7849)
* [X] Refactors to...
  * [X] [Extract constants/locals in refactorings](https://github.com/Microsoft/TypeScript/pull/18783)
  * [X] [Convert JSDoc type annotations to TypeScript](https://github.com/Microsoft/TypeScript/pull/18747)
* [X] Quick fixes to...
  * [X] [Infer from usage on `noImplicitAny` errors](https://github.com/Microsoft/TypeScript/pull/14786)
  * [X] [Invoke uncalled decorators](https://github.com/Microsoft/TypeScript/pull/18969)
  * [X] [Install from `@types`](https://github.com/Microsoft/TypeScript/issues/14423)
* [X] [Support outlining for `// #regions`](https://github.com/Microsoft/TypeScript/issues/11073)

# 2.5 (August 2017)

* [X] [Assertion/cast JSDoc syntax in `checkJs` mode](https://github.com/Microsoft/TypeScript/issues/5158)
* [x] [Refactoring to extract functions & methods](https://github.com/Microsoft/TypeScript/pull/16960)
* [X] [Optional `catch` binding](https://github.com/Microsoft/TypeScript/issues/17467)
* [X] Quick fixes to...
    * [X] [correct references to types of properties](https://github.com/Microsoft/TypeScript/pull/17462)
    * [X] [correct uses of JSDoc types to TypeScript types](https://github.com/Microsoft/TypeScript/pull/17250)

# 2.4 (June 2017)

* [X] [Infer from generic function return types](https://github.com/Microsoft/TypeScript/pull/16072)
* [X] [Contextual generic function types](https://github.com/Microsoft/TypeScript/pull/16305)
* [X] [Stricter generic signature checks](https://github.com/Microsoft/TypeScript/pull/16368)
* [X] [Covariant checking for callback parameters](https://github.com/Microsoft/TypeScript/pull/15104)
* [X] [String valued members in enums](https://github.com/Microsoft/TypeScript/pull/15486)
* [X] [Weak type detection](https://github.com/Microsoft/TypeScript/pull/16047)
* [X] [ES dynamic `import()` expressions](https://github.com/Microsoft/TypeScript/pull/14774)
* [X] [Refactoring support in Language Service API](https://github.com/Microsoft/TypeScript/pull/15569)
    * [Refactor ES5 function to ES6 class in .js files](https://github.com/Microsoft/TypeScript/pull/15569)
* [X] [Suggestions and quick fixes for spelling corrections in error messages](https://github.com/Microsoft/TypeScript/pull/15507)

# 2.3 (April 2017)

* [X] [Generator support for ES3/ES5](https://github.com/Microsoft/TypeScript/issues/1564)
* [X] [Asynchronous iterators](https://github.com/Microsoft/TypeScript/pull/12346)
* [X] [Generic defaults](https://github.com/Microsoft/TypeScript/pull/13487)
* [X] [Controlling `this` in methods of object literals through contextual type](https://github.com/Microsoft/TypeScript/pull/14141)
* [X] [JSX stateless components overload resolution](https://github.com/Microsoft/TypeScript/issues/9703)
* [X] [JSX `children` checking](https://github.com/Microsoft/TypeScript/pull/15160)
* [X] [New `--strict` master option](https://github.com/Microsoft/TypeScript/pull/14486)
* [X] [Report errors in .js files with new `--checkJs`](https://github.com/Microsoft/TypeScript/pull/14496)
* [X] [Enhanced `tsc --init` output](https://github.com/Microsoft/TypeScript/pull/13982)
* [X] [Language Service Extensibility](https://github.com/Microsoft/TypeScript/issues/6508)

# 2.2 (February 2017)

* [X] [Mixin classes](https://github.com/Microsoft/TypeScript/pull/13743)
* [X] [Allow deriving from object and intersection types](https://github.com/Microsoft/TypeScript/pull/13604)
* [X] [Support for `new.target`](https://github.com/Microsoft/TypeScript/issues/2551)
* [X] [Improved checking of nullable operands in expressions](https://github.com/Microsoft/TypeScript/pull/13483)
* [X] [Update `__extends` to use `Object.setPrototypeOf`](https://github.com/Microsoft/TypeScript/pull/12488)
* [X] [Allow property (dotted) access for types with string index signatures](https://github.com/Microsoft/TypeScript/issues/12596)
* [X] [Support for JSX spread children](https://github.com/Microsoft/TypeScript/issues/9495)
* [X] New [`--jsx react-native`](https://github.com/Microsoft/TypeScript/issues/11158)
* [X] [Support for the `object` type.](https://github.com/Microsoft/TypeScript/issues/1809)
* [X] More Quick Fixes!
    * [Add missing imports](https://github.com/Microsoft/TypeScript/pull/11768)
    * [Implement interface/abstract class members](https://github.com/Microsoft/TypeScript/pull/11547)
    * [Remove unused declarations](https://github.com/Microsoft/TypeScript/pull/11546)
    * [Add missing `this.`](https://github.com/Microsoft/TypeScript/pull/13759)
    * [Add missing property declaration](https://github.com/Microsoft/TypeScript/pull/14097)

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
