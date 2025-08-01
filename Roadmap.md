This page outlines specific features and fixes that are scheduled or planned for given releases. The 6-month roadmaps that outlines focus areas of work can be viewed for

* [January to June 2021](https://github.com/microsoft/TypeScript/issues/42673) (current)
* [January to December 2020](https://github.com/microsoft/TypeScript/issues/36948)
* [July to December 2019](https://github.com/microsoft/TypeScript/issues/33118)
* [January to June 2019](https://github.com/Microsoft/TypeScript/issues/29288)

- [X] Feature already available in [`typescript@next`](https://github.com/Microsoft/TypeScript-Handbook/blob/master/pages/Nightly%20Builds.md)

# Future

* Investigate [nominal typing support](https://github.com/Microsoft/TypeScript/issues/202)
* [Flattening declarations](https://github.com/Microsoft/TypeScript/issues/4433)
* Implement ES Decorator proposal
* Investigate [Ambient](https://github.com/Microsoft/TypeScript/issues/2900), [Deprecated](https://github.com/Microsoft/TypeScript/issues/390), and [Conditional](https://github.com/Microsoft/TypeScript/issues/3538) decorators
* [Investigate partial type argument inference](https://github.com/Microsoft/TypeScript/pull/26349)
* Quick fix to [Scaffold local `@types` packages](https://github.com/Microsoft/TypeScript/issues/25746)
* [Investigate error messages in haiku or iambic pentameter](https://twitter.com/kitsonk/status/973651805950242816)
* [Decorators for function expressions/arrow functions](https://github.com/microsoft/TypeScript/issues/7318)

# 5.0 (March 2023)

[See the 5.0 Iteration Plan for more details.](https://github.com/microsoft/TypeScript/issues/51362)

* [x] [Standard ECMAScript Decorators](https://github.com/microsoft/TypeScript/pull/50820)
* [x] [`const` Type Parameters](https://github.com/microsoft/TypeScript/pull/51865)
* [x] [`--moduleResolution bundler`](https://github.com/microsoft/TypeScript/pull/51669)
* [x] [`extends` Support for Multiple `tsconfig.json` Files](https://github.com/microsoft/TypeScript/pull/50403)
* [x] [Declaration Files for non-JavaScript Files with `--allowArbitraryExtensions`](https://github.com/microsoft/TypeScript/issues/50133)
* [x] [`export type *`](https://github.com/microsoft/TypeScript/pull/52217)
* [x] [`--verbatimModuleSyntax`](https://github.com/microsoft/TypeScript/issues/51479)
* [x] [`@satisfies` JSDoc Tag in JavaScript](https://github.com/microsoft/TypeScript/pull/51753)
* [x] [`@overload` JSDoc Tag in JavaScript](https://github.com/microsoft/TypeScript/pull/51234)
* [x] [Unification Between Numeric and Literal `enum`s](https://github.com/microsoft/TypeScript/pull/50528)
* [x] [Toggling Certain Compiler-Specific Flags Under `--build`](https://github.com/microsoft/TypeScript/pull/51241)
* [x] [Flag Deprecations](https://github.com/microsoft/TypeScript/issues/51000)
* [x] [Improved `in` Checks Under `--noUncheckedIndexedAccess`](https://github.com/microsoft/TypeScript/pull/51653)
* [x] [Editor Support for Case-Sensitive Import Sorting](https://github.com/microsoft/TypeScript/pull/51733)
* [x] [Editor Support for Exhaustive `switch`/`case` Snippet Completions](https://github.com/microsoft/TypeScript/pull/50996)
* [x] [Virtual File System Support for TSServer](https://github.com/microsoft/TypeScript/issues/47600)
* Optimizations
  * [x] [Migrate to Modules](https://github.com/microsoft/TypeScript/pull/51387)
  * [x] [`Node` Monomorphization](https://github.com/microsoft/TypeScript/pull/51682)
  * [x] [`Symbol` Monomorphization](https://github.com/microsoft/TypeScript/pull/51880)
  * [x] [`Identifier` Size Reduction](https://github.com/microsoft/TypeScript/pull/52170)
  * [x] [`Printer` Caching](https://github.com/microsoft/TypeScript/pull/52382)
  * [x] [Limited TDZ Avoidance](https://github.com/microsoft/TypeScript/issues/52924)

# 4.9 (November 2022)

[See the 4.9 Iteration Plan for more details.](https://github.com/microsoft/TypeScript/issues/50457)

* [x] [Narrowing Checks for Unknown Properties with the `in` Operator](https://github.com/microsoft/TypeScript/issues/21732)
* [x] [`satisfies` operator](https://github.com/microsoft/TypeScript/issues/47920)
* [x] [Auto-accessors in classes](https://github.com/microsoft/TypeScript/pull/49705)
* [x] [Forbidden Equality Checks Against `NaN`](https://github.com/microsoft/TypeScript/pull/50626)
* [x] [File Watching Defaults Using to File-System Events](https://github.com/microsoft/TypeScript/pull/50366)
* [x] ["Remove Imports" Command for Editors](https://github.com/microsoft/TypeScript/pull/50931)
* [x] [Go-to-Definition on `return` Keywords](https://github.com/microsoft/TypeScript/pull/51227)
* Optimizations
  * [`forEachChild` as a Jump Table](https://github.com/microsoft/TypeScript/pull/50225)
  * [`visitEachChild` as a Jump Table](https://github.com/microsoft/TypeScript/pull/50266)
  * [Lazily Intersect and Normalize Substitution Type Constraints](https://github.com/microsoft/TypeScript/pull/50397)

# 4.8 (August 2022)

[See the 4.8 Iteration Plan for more details.](https://github.com/microsoft/TypeScript/issues/49074)

* [x] [Improved Narrowing on Generics and Intersection Reduction](https://github.com/microsoft/TypeScript/pull/49119)
* [x] [Improved Inference for `infer` Types in Template String Types](https://github.com/microsoft/TypeScript/pull/48094)
* [x] [`--build`, `--watch`, and `--incremental` Performance Improvements](https://github.com/microsoft/TypeScript/pull/48784)
* [x] [Errors When Comparing Object and Array Literals](https://github.com/microsoft/TypeScript/pull/45978)
* [x] [Improved Inference from Binding Patterns](https://github.com/microsoft/TypeScript/pull/49086)
* [x] [Resilient File-Watching During Large File Changes](https://github.com/microsoft/TypeScript/pull/48997)
* [x] [Find-All-References Performance Improvements](https://github.com/microsoft/TypeScript/pull/49581)

# 4.7 (May 2022)

[See the 4.7 Iteration Plan for more details.](https://github.com/microsoft/TypeScript/issues/48027)

* [x] [`--module` and `--moduleResolution` options `node16` and `nodenext`](https://github.com/microsoft/TypeScript/pull/44501)
* [x] [Control Flow and Class Initialization Checks for Computed Properties](https://github.com/microsoft/TypeScript/pull/45974)
* [x] [Instantiation Expressions](https://github.com/microsoft/TypeScript/pull/47607)
* [x] [Improved Inference Between Function-Initialized Properties and Methods](https://github.com/microsoft/TypeScript/pull/48538)
* [x] [`extends` Constraints on `infer` Type Variables](https://github.com/microsoft/TypeScript/pull/48112)
* [x] [Optional Variance Annotations](https://github.com/microsoft/TypeScript/pull/48240)
* [x] [`moduleSuffixes` Option For Resolution](https://github.com/microsoft/TypeScript/pull/48189)
* [x] [`resolution-mode` for `/// <reference types="..." />` Directives](https://github.com/microsoft/TypeScript/pull/47732)
* [x] [`resolution-mode` for `import type`](https://github.com/microsoft/TypeScript/pull/47807)
* [x] [Allow `typeof` on `#private` Fields](https://github.com/microsoft/TypeScript/pull/47696)
* [x] [Object Method Snippet Completions](https://github.com/microsoft/TypeScript/issues/46590)
* [x] [Grouping-Aware Organize Imports](https://github.com/microsoft/TypeScript/pull/48330)
* [x] [Go to Source Definition/JavaScript Implementation](https://github.com/microsoft/TypeScript/pull/48264)

# 4.6 (February 2022)

[See the 4.6 Iteration Plan for more details.](https://github.com/microsoft/TypeScript/issues/46858)

* [x] [Narrowing for Variables Destructured from Discriminated Unions](https://github.com/microsoft/TypeScript/pull/46266)
* [x] [Relaxed Checks on Code Before `super()`](https://github.com/microsoft/TypeScript/pull/29374)
* [x] [Improved Recursion Depth Checks](https://github.com/microsoft/TypeScript/pull/46599)
* [x] [Indexed Access Inference Improvements](https://github.com/microsoft/TypeScript/pull/47109)
* [x] [`--target es2022`](https://github.com/microsoft/TypeScript/pull/46291)
* [x] [Syntax and Binding Errors in JavaScript Files](https://github.com/microsoft/TypeScript/pull/4706)
* [x] [Release of `@typescript/analyze-trace`](https://github.com/microsoft/typescript-analyze-trace)

# 4.5 (November 2021)

[See the 4.5 Iteration Plan for more details.](https://github.com/microsoft/TypeScript/issues/45418)

* [x] [`lib` Overrides from `node_modules`](https://github.com/microsoft/TypeScript/pull/45771)
* [x] [`Awaited` and `Promise` Function Improvements](https://github.com/microsoft/TypeScript/pull/45350)
* [x] [Tail Recursive Evaluation of Conditional Types](https://github.com/microsoft/TypeScript/pull/45711)
* [x] [Template String Discriminants](https://github.com/microsoft/TypeScript/pull/46137)
* [x] [`--module es2022`](https://github.com/microsoft/TypeScript/pull/44656)
* [x] [Controls for Import Elision via `--preserveValueImports`](https://github.com/microsoft/TypeScript/pull/44619)
* [x] [`type` Modifiers on Import Names](https://github.com/microsoft/TypeScript/pull/45998)
* [x] [`in` Checks on `#private` Fields](https://github.com/microsoft/TypeScript/pull/44648)
* [x] [Import Assertions](https://github.com/microsoft/TypeScript/pull/40698)
* [x] [Speed Improvements on Windows/Mac via `realpathSync.native`](https://github.com/microsoft/TypeScript/pull/44966)
* [x] [Snippet Completions for Subclass Methods](https://github.com/microsoft/TypeScript/issues/45670)
* [x] [Snippet Completions for JSX Attributes](https://github.com/microsoft/TypeScript/pull/45903)
* [x] [Better Editor Support for Unresolved Types](https://github.com/microsoft/TypeScript/pull/45976)

# 4.4 (August 2021)

[See the 4.4 Iteration Plan for more details.](https://github.com/microsoft/TypeScript/issues/44237)

* [x] [Control Flow Analysis of Aliased Conditions](https://github.com/microsoft/TypeScript/pull/44730)
* [x] [Generalized index signatures](https://github.com/microsoft/TypeScript/pull/26797)
* [x] [Strict optional property checks with `--strictOptionalProperties`](https://github.com/microsoft/TypeScript/issues/13195)
* [x] [Use `unknown` for `try`/`catch` variables with `--useUnknownInCatchVariables`](https://github.com/microsoft/TypeScript/pull/41013)
* [x] [Updated `--help` descriptions](https://github.com/microsoft/TypeScript/pull/44409#event-4893868354)
* [x] [Inlay Hints](https://github.com/microsoft/TypeScript/pull/42089)
* [x] [High-Confidence Suggestions in Unchecked JavaScript](https://github.com/microsoft/TypeScript/issues/41582)
* [x] [Quick Fix to Declare Missing Properties on Objects](https://github.com/microsoft/TypeScript/pull/44576)

# 4.3 (May 2021)

[See the 4.3 Iteration Plan for more details.](https://github.com/microsoft/TypeScript/issues/42762)

* [x] [Separate "write" types](https://github.com/microsoft/TypeScript/pull/42425)
* [x] [`--noImplicitOverride` and the `override` keyword](https://github.com/microsoft/TypeScript/pull/39669)
* [x] [Template string type inference and compatibility improvements](https://github.com/microsoft/TypeScript/pull/43361)
* [x] [Contextual Narrowing for Generics](https://github.com/microsoft/TypeScript/pull/43183)
* [x] [Infer template string types when contextually typed](https://github.com/microsoft/TypeScript/pull/43376)
* [x] [ECMAScript `#private` Methods/Statics/Accessors](https://github.com/microsoft/TypeScript/pull/42458)
* [x] [`static` Index Signatures](https://github.com/microsoft/TypeScript/pull/37797)
* [x] [Editor support for `@link` tags](https://github.com/microsoft/TypeScript/pull/41877)
* Investigate auto-import improvements
    * [x] [Complete import statements through auto-imports](https://github.com/microsoft/TypeScript/issues/31658)
* [Support Go-to-Definition on Relative Non-JS/TS Files](https://github.com/microsoft/TypeScript/pull/42539)

# 4.2 (February 2021)

[See the 4.2 Iteration Plan for more details.](https://github.com/microsoft/TypeScript/issues/41601)

* [x] [Leading/middle rest elements in tuple types](https://github.com/microsoft/TypeScript/pull/41544)
* [x] [Smarter Type Alias Printing and Emit](https://github.com/microsoft/TypeScript/pull/42284)
* [x] ~[Template Literal Types for Template String Expressions](https://github.com/microsoft/TypeScript/pull/41891)~
* [x] [`--noPropertyAccessFromIndexSignature`](https://github.com/microsoft/TypeScript/pull/40171/)
* [x] [`abstract` constructor types](https://github.com/microsoft/TypeScript/pull/36392)
* [x] [Smarter unused local analysis in destructuring](https://github.com/microsoft/TypeScript/issues/31388)
* [x] [`--explainFiles` to understand why files have been included in a program](https://github.com/microsoft/TypeScript/pull/40011)
* [x] [Declare missing helper function](https://github.com/microsoft/TypeScript/pull/41215)

# 4.1. (November 2020)

[See the 4.1 Iteration Plan for more details](https://github.com/microsoft/TypeScript/issues/40124)

* [x] [Template literal types and mapped type 'as' clauses](https://github.com/microsoft/TypeScript/pull/40336)
* [x] [Recursive Conditional Types](https://github.com/microsoft/TypeScript/pull/40002)
* [x] [Pedantic Index Signature Checks (`--noUncheckedIndexedAccess`)](https://github.com/microsoft/TypeScript/issues/13778)
* [x] [`export * as default`](https://github.com/microsoft/TypeScript/issues/39769)
* [x] [Aliased `this` in JavaScript functions](https://github.com/microsoft/TypeScript/issues/39842)
* [x] [Editor support for `/** @see */` tags](https://github.com/microsoft/TypeScript/issues/35524)
* [x] [Semantic Highlighting API](https://github.com/microsoft/TypeScript/issues/38435)

# 4.0 (August 2020)

[See the 4.0 Iteration Plan for more details.](https://github.com/microsoft/TypeScript/issues/38510)

* [x] [Variadic Tuple Types](https://github.com/microsoft/TypeScript/pull/39094)
* [x] [Labeled Tuple Elements](https://github.com/Microsoft/TypeScript/issues/28259)
* [x] [Class Property Inference from Constructors](https://github.com/microsoft/TypeScript/pull/37920)
* [x] [Short-Circuiting Assignment Operators](https://github.com/microsoft/TypeScript/issues/37255)
* [x] [`unknown` on `catch` Clause Bindings](https://github.com/microsoft/TypeScript/issues/36775)
* [x] [Lightweight Editor Mode](https://github.com/microsoft/TypeScript/issues/39035)
* [x] [Improved Auto-Import](https://github.com/microsoft/TypeScript/issues/37812)
* [x] [Editor support for `/** @deprecated */` tags](https://github.com/microsoft/TypeScript/issues/390)
* [x] [Refactoring Discoverability](https://github.com/microsoft/TypeScript/issues/34930) ([ideas](https://github.com/microsoft/TypeScript/issues/37895))
* [x] Refactorings
    * [x] [Convert to Optional Chaining and Coalescing](https://github.com/microsoft/TypeScript/issues/35018)

# 3.9 (May 2020)

[See the 3.9 Iteration Plan for more details.](https://github.com/microsoft/TypeScript/issues/37198)

* [x] [Fix Inference in `Promise.all`](https://github.com/microsoft/TypeScript/pull/34501)
* [x] Type-Checking Speedups
  * https://github.com/microsoft/TypeScript/pull/36576
  * https://github.com/microsoft/TypeScript/pull/36590
  * https://github.com/microsoft/TypeScript/pull/36607
  * https://github.com/microsoft/TypeScript/pull/36622
  * https://github.com/microsoft/TypeScript/pull/36754
  * https://github.com/microsoft/TypeScript/pull/36696
* [x] [`// @ts-expect-error` Comments](https://github.com/microsoft/TypeScript/pull/36014)
* [x] [Uncalled Function Checks in Conditionals](https://github.com/microsoft/TypeScript/issues/36048)
* [x] [Support for CommonJS Auto-Imports](https://github.com/microsoft/TypeScript/pull/37027)
* [x] [Preserved Newlines in Refactorings/Quick Fixes](https://github.com/microsoft/TypeScript/pull/36688)
* [x] [Editor Support for Solution-Style `tsconfig.json` Files](https://github.com/microsoft/TypeScript/pull/37239)
* [x] [Editor Support for Multi-Project Symbol Navigation](https://github.com/microsoft/TypeScript/pull/38027)
* [x] Quick Fixes to
  * [x] [Correct Cases of Missing Return Expressions](https://github.com/microsoft/TypeScript/pull/26434)
  * [ ] [Declare a `private` method](https://github.com/microsoft/TypeScript/issues/37782)
  * [x] [Declare a `private` property](https://github.com/microsoft/TypeScript/issues/36249)

# 3.8 (February 2020)

[See the 3.8 Iteration Plan for more details.](https://github.com/microsoft/TypeScript/issues/34898)

* [x] [Type-Only Imports and Exports](https://github.com/microsoft/TypeScript/pull/35200)
* [x] [New `export * as ns` Syntax](https://github.com/microsoft/TypeScript/pull/34903)
* [x] [Top-Level Await](https://github.com/microsoft/TypeScript/issues/25988)
* [x] [Private Fields](https://github.com/Microsoft/TypeScript/pull/30829)
* [x] [JSDoc `@public`/`@private`/`@protected` Modifiers](https://github.com/microsoft/TypeScript/pull/35731)
* [x] [JSDoc `@readonly` Modifier](https://github.com/microsoft/TypeScript/issues/17233)
* [x] [Improved File Watching and `watchOptions`](https://github.com/microsoft/TypeScript/pull/35615)
* [x] ["Fast and Loose" Incremental Checking](https://github.com/microsoft/TypeScript/issues/33329)
* [x] [Editor Support for Call Hierarchy View](https://github.com/microsoft/TypeScript/issues/31863)

# 3.7 (November 2019)

[See the 3.7 Iteration Plan for more details.](https://github.com/microsoft/TypeScript/issues/33352)

* [x] [Optional Chaining](https://github.com/microsoft/TypeScript/pull/33294)
* [x] [Nullish Coalescing](https://github.com/microsoft/TypeScript/issues/26578)
* [X] [Assertions in Control Flow Analysis](https://github.com/microsoft/TypeScript/pull/32695)
* [X] [Recursive Type References](https://github.com/microsoft/TypeScript/pull/33050)
* [x] [Allow `allowJs` and `declaration` to be used together](https://github.com/microsoft/TypeScript/pull/32372)
* [x] [Uncalled Function Checks](https://github.com/microsoft/TypeScript/pull/33178)
* [x] [`--declaration` and `--isolatedModules`](https://github.com/Microsoft/TypeScript/issues/29490)
* [x] [Flattened Property/Method Reporting](https://github.com/microsoft/TypeScript/issues/33361)
* [x] [`--declaration` and `--allowJs`](https://github.com/Microsoft/TypeScript/issues/7546)
* [x] Editor Functionality/APIs
  * [x] [Build-Free Editing for Project References](https://github.com/microsoft/TypeScript/pull/32028)
  * [x] [Auto-inserted `await` for completions](https://github.com/microsoft/TypeScript/issues/31450)
  * [x] [Quick fix to convert `const` to `let`](https://github.com/microsoft/TypeScript/issues/22473)
  * [x] [Semicolon Formatting Preferences](https://github.com/microsoft/TypeScript/issues/19882)

# 3.6 (August 2019)

[See the 3.6 Iteration Plan for more details.](https://github.com/microsoft/TypeScript/issues/31639)

* [x] [Strongly typed iterators and generators](https://github.com/Microsoft/TypeScript/issues/2983)
* [x] [More accurate array spreads](https://github.com/microsoft/TypeScript/pull/31166)
* [x] [Improved UX around Promises](https://github.com/microsoft/TypeScript/issues/30646)
* [x] [Semicolon-aware auto-imports](https://github.com/microsoft/TypeScript/issues/19882)

# 3.5 (May 2019)

[See the 3.5 Iteration Plan for more details.](https://github.com/Microsoft/TypeScript/issues/30555)

* [X] [Higher order inferences for constructor functions](https://github.com/Microsoft/TypeScript/pull/31116)
* [X] [Easier assignment to discriminated unions](https://github.com/Microsoft/TypeScript/pull/30779)
* [X] [Change default constraint for type parameters from `{}` to `unknown`](https://github.com/Microsoft/TypeScript/pull/30637)
* [X] [Improved checking of indexed access types](https://github.com/microsoft/TypeScript/pull/30769)
* [X] [The `Omit` helper type](https://github.com/Microsoft/TypeScript/pull/30552)
* [x] [APIs for composite projects (`--build` mode)](https://github.com/Microsoft/TypeScript/issues/29978)
* [X] [`--allowUmdGlobalAccess` to use UMD globals from modules](https://github.com/Microsoft/TypeScript/pull/30776)
* [X] [`--noEmitOnError` with `--isolatedModules`](https://github.com/microsoft/TypeScript/pull/31043)
* [X] Speed improvements in `--incremental` and `--build`
  * [X] [Caching module resolution results](https://github.com/Microsoft/TypeScript/pull/31100)
  * [X] [Caching results of parsing `tsconfig.json`](https://github.com/Microsoft/TypeScript/pull/31101)
* [X] [Sorted/prioritized completions](https://github.com/Microsoft/TypeScript/issues/15024)
* [X] [Smart Select API](https://github.com/Microsoft/TypeScript/issues/29071)
* [X] [Refactoring to extract type](https://github.com/Microsoft/TypeScript/issues/23869)

# 3.4 (March 2019)

[See the 3.4 Iteration Plan for more details.](https://github.com/Microsoft/TypeScript/issues/30281)

* [X] [Higher order function type inference](https://github.com/Microsoft/TypeScript/pull/30215)
* [X] [`--incremental` builds with `.tsbuildinfo` files](https://github.com/Microsoft/TypeScript/pull/29813)
* [X] [Improved support for read-only arrays and tuples](https://github.com/Microsoft/TypeScript/pull/29435)
* [X] [Const contexts for literal expressions](https://github.com/Microsoft/TypeScript/pull/29510)
* [X] [`globalThis`](https://github.com/Microsoft/TypeScript/pull/29332)
* [X] Refactorings to...
  * [X] [Convert to "named" parameters](https://github.com/Microsoft/TypeScript/issues/23552)

# 3.3 (January 2019)

* [X] [Relaxed rules on methods of union types](https://github.com/Microsoft/TypeScript/pull/29011)
* [X] [File-incremental builds in `--build --watch` mode for composite projects](https://github.com/Microsoft/TypeScript/pull/29161)

# 3.2 (November 2018)

* [X] [Strict bind, call, and apply methods on functions](https://github.com/Microsoft/TypeScript/pull/27028)
* [X] [Allow non-unit types in union discriminants](https://github.com/Microsoft/TypeScript/pull/27695)
* [X] [Generic spread expressions in object literals](https://github.com/Microsoft/TypeScript/pull/28234)
* [X] [Generic object rest variables and parameters](https://github.com/Microsoft/TypeScript/pull/28312)
* [X] [Type-checking support for `BigInt`](https://github.com/Microsoft/TypeScript/issues/15096)
* [X] [Configuration inheritance through node packages](https://github.com/Microsoft/TypeScript/pull/27348)
* [X] [Supporting `Object.defineProperty` property assignments in JS](https://github.com/Microsoft/TypeScript/pull/27208)
* [X] [Support printing the implied configuration object to the console with `--showConfig`](https://github.com/Microsoft/TypeScript/pull/27353)
* [X] [Improved formatting and indentation for lists and chained calls](https://github.com/Microsoft/TypeScript/pull/28340)
* [X] Quick fixes to...
  * [X] [Add intermediate `unknown` type assertions](https://github.com/Microsoft/TypeScript/issues/28067)
  * [X] [Add missing `new` keyword](https://github.com/Microsoft/TypeScript/issues/26580)
  * [X] [Infer types from usage outside of `noImplicitAny`](https://github.com/Microsoft/TypeScript/pull/27693)
  * [X] [Infer JSDoc types from usage](https://github.com/Microsoft/TypeScript/pull/27610)

# 3.1 (September 2018)

* [X] [Mapped tuple and array types](https://github.com/Microsoft/TypeScript/pull/26063)
* [X] [Property assignments on function declarations](https://github.com/Microsoft/TypeScript/pull/26368)
* [X] [`typesVersions` redirects](https://github.com/Microsoft/TypeScript/issues/22605)
* [X] [Error UX improvements](https://github.com/Microsoft/TypeScript/issues/26077)
* [X] Refactorings to...
  * [X] [Rename files from import/export paths](https://github.com/Microsoft/TypeScript/issues/24501)
  * [X] [Convert from `Promise#then`/`catch` to `async`/`await`](https://github.com/Microsoft/TypeScript/pull/26373)

# 3.0 (July 2018)

* [X] [Support for project references/composite projects](https://github.com/Microsoft/TypeScript/issues/3469#issuecomment-400439520)
* [X] [Tuples in rest parameters and spread expressions](https://github.com/Microsoft/TypeScript/pull/24897)
* [X] [New `unknown` top type](https://github.com/Microsoft/TypeScript/pull/24439)
* [X] [`/// <reference lib="..." />` reference directives](https://github.com/Microsoft/TypeScript/pull/23893)
* [X] [Support for `defaultProps` and `propTypes` in React JSX elements](https://github.com/Microsoft/TypeScript/pull/24422)
* [X] Error UX improvements
  * [X] [Related error spans](https://github.com/Microsoft/TypeScript/issues/25257)
  * [X] [Improved message quality and suggestions](https://github.com/Microsoft/TypeScript/issues/25310)
* [X] Refactors to...
  * [X] [Convert named imports to namespace imports and back](https://github.com/Microsoft/TypeScript/pull/24469)
  * [X] [Add or remove braces from arrow function](https://github.com/Microsoft/TypeScript/pull/23423)
* [X] Quick fixes to...
  * [X] [Remove unused labels](https://github.com/Microsoft/TypeScript/pull/24037)
  * [X] [Remove unreachable code](https://github.com/Microsoft/TypeScript/pull/24028)
* [X] [Outlining spans for JSX expressions](https://github.com/Microsoft/TypeScript/issues/23273)
* [X] [Auto-closing JSX tags](https://github.com/Microsoft/TypeScript/issues/20811)

# 2.9 (May 2018)

* [X] [Support number and symbol named properties with keyof and mapped types](https://github.com/Microsoft/TypeScript/pull/23592)
* [X] [Support for passing generics to JSX elements](https://github.com/Microsoft/TypeScript/pull/22415)
* [X] [Support for passing generics to tagged template calls](https://github.com/Microsoft/TypeScript/pull/23430)
* [X] [Allow `import(...)`-ing types at any location](https://github.com/Microsoft/TypeScript/issues/14844)
* [X] [`--pretty` error output by default](https://github.com/Microsoft/TypeScript/pull/23408)
* [X] [Support for `import.meta`](https://github.com/Microsoft/TypeScript/pull/23327)
* [X] New [`--resolveJsonModule`](https://github.com/Microsoft/TypeScript/pull/22167)
* [X] [Declaration source maps and code navigation via them](https://github.com/Microsoft/TypeScript/pull/22658)
* [X] [Show suggestion diagnostics for open files](https://github.com/Microsoft/TypeScript/pull/22204)
* [X] [Show unused declarations as suggestions](https://github.com/Microsoft/TypeScript/pull/22361)
* [X] [Support setting quote style in quick fixes and refactorings](https://github.com/Microsoft/TypeScript/pull/22236)
* [X] New [Rename File](https://github.com/Microsoft/TypeScript/pull/23573) command
* [X] Refactors to...
  * [X] [Move to a new file](https://github.com/Microsoft/TypeScript/pull/23726)
  * [X] [Convert property to getter/setter](https://github.com/Microsoft/TypeScript/pull/22143)
* [X] Quick fix to...
  * [X] [Convert `require` to `import` in `.ts` files](https://github.com/Microsoft/TypeScript/pull/23711)
  * [X] [Install `@types/node` for built-in node modules](https://github.com/Microsoft/TypeScript/pull/23807)

# 2.8 (March 2018)

* [X] [Conditional types](https://github.com/Microsoft/TypeScript/pull/21316)
* [X] [Type inference in conditional types](https://github.com/Microsoft/TypeScript/pull/21496)
* [X] [Predefined conditional types in `lib.d.ts`](https://github.com/Microsoft/TypeScript/pull/21847)
* [X] [Improved control over mapped type modifiers](https://github.com/Microsoft/TypeScript/pull/21919)
* [X] [Improved `keyof` with intersection types](https://github.com/Microsoft/TypeScript/pull/22300)
* [X] New [`--emitDeclarationOnly`](https://github.com/Microsoft/TypeScript/pull/20735)
* [X] [Better handling for namespace-patterns in .js files](https://github.com/Microsoft/TypeScript/issues/7632)
* [X] [Support for per-file JSX pragmas](https://github.com/Microsoft/TypeScript/pull/21218)
* [X] New [Organize imports](https://github.com/Microsoft/TypeScript/pull/21909) command
* [X] [Quick fix for uninitialized class properties](https://github.com/Microsoft/TypeScript/issues/21509)

# 2.7 (January 2018)

* [X] [Support for properties named with `const`-declared symbols](https://github.com/Microsoft/TypeScript/pull/15473)
* [X] [Strict property initialization checks in classes](https://github.com/Microsoft/TypeScript/pull/20075)
* [X] [Definite assignment assertions](https://github.com/Microsoft/TypeScript/pull/20166)
* [X] [Improved type inference for object literals](https://github.com/Microsoft/TypeScript/pull/19513)
* [X] [Improved handling of structurally identical classes](https://github.com/Microsoft/TypeScript/pull/19671)
* [X] [Fixed length tuples](https://github.com/Microsoft/TypeScript/pull/17765)
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
* [x] [Support for JSX Fragments](https://github.com/Microsoft/TypeScript/pull/19249)
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
