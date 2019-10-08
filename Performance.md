There are easy ways to configure TypeScript to ensure faster compilations and editing experiences.
The earlier on these practices are adopted, the better.
Beyond best-practices, there are some common techniques for investigating slow compilations/editing experiences, some common fixes, and some common ways of helping the TypeScript team investigate the issues as a last resort.

# Using Project References

When building up any codebase of a non-trivial size with TypeScript, it is helpful to organize the codebase into several independent *projects*.
Each project has its own `tsconfig.json` that has dependencies on other projects.
This can be helpful to avoid loading too many files in a single compilation, and also makes certain codebase layout strategies easier to put together.

There are some very basic ways of breaking up a codebase into projects.
As an example, one might be a program with a project for the client, a project for the server, and a project that's shared between the two.

```
              ------------
              |          |
              |  Shared  |
              ^----------^
             /            \
            /              \
------------                ------------
|          |                |          |
|  Client  |                |  Server  |
-----^------                ------^-----
```

Tests can also be broken into their own project.

```
              ------------
              |          |
              |  Shared  |
              ^-----^----^
             /      |     \
            /       |      \
------------  ------------  ------------
|          |  |  Shared  |  |          |
|  Client  |  |  Tests   |  |  Server  |
-----^------  ------------  ------^-----
     |                            |
     |                            |
------------                ------------
|  Client  |                |  Server  |
|  Tests   |                |  Tests   |
------------                ------------
```

You can read up more about project references here. <!-- TODO -->

# Configuring `tsconfig.json`

Within a `tsconfig.json`, there are two ways to specify files in a project:

* the `files` list
* the `include` and `exclude` lists

The primary difference between the two is that `files` expects a list of file paths to source files, and `include/`exclude` use globbing patterns to match against files.

While specifying `files` will allow TypeScript to quickly load up files up directly, it can be cumbersome if you have many files in your project without just a few top-level entry-points.
Additionally, it's easy to forget to add new files to your `tsconfig.json`, which means that you might end up with strange editor behavior where those new files are incorrectly analyzed.
All this can be cumbersome.

`include`/`exclude` help avoid needing to specify these files, but at a cost: files must be discovered by walking through included directories.
When running through a *lot* of folders, this can slow compilations down.
Additionally, sometimes a compilation will include lots of unnecessary `.d.ts` files and test files, which can increase compilation time and memory overhead.
Finally, while `exclude` has some reasonable defaults, certain configurations like mono-repos mean that a "heavy" folders like `node_modules` can still end up being included.

For best practices, we recommend the following:

* Specify only source folders in your project.
* Don't mix source files from other projects in the same folder.
* If keeping tests in the same folder as other source files, give them a distinct name so they can easily be excluded.
* Avoid large build artifacts and dependency folders like `node_modules` in source directories

Here is a reasonable `tsconfig.json` that demonstrates this in action.

```json5
{
    "compilerOptions": {
        // ...
    },
    "include": ["src"],
    "exclude": ["**/node_modules", "**/.*/"],
}
```

## Incremental Project Emit

The `--incremental` flag allows TypeScript to save state from the last compilation to a `.tsbuildinfo` file.
This file is used to figure out the smallest set of files that might to be re-checked/re-emitted since it last ran, much like how TypeScript's `--watch` mode works.

Incremental compiles are enabled by default when using the `composite` flag for project references, but can bring the same speed-ups for any project that opts in.

## Skipping `.d.ts` Checking

By default, TypeScript performs a full re-check of all `.d.ts` files in a project to find issue and inconsistencies; however, this is typically unnecessary.
Most of the time, the `.d.ts` files are known to already work - the way that types extend each other was already verified once, and declarations that matter will be checked anyway.

TypeScript provides the option to skip type-checking of the `.d.ts` files that it ships with (e.g. `lib.d.ts`) using the `skipDefaultLibCheck` flag.

Alternatively, you can also enable the `skipLibCheck` flag to skip checking *all* `.d.ts` files in a compilation.

These two options can often hide misconfiguration and conflicts in `.d.ts` files, so we suggest using them sparingly.

# Configuring Other Build Tools

TypeScript compilation is often performed with other build tools in mind - especially when writing web apps that might involve a bundler.
While we can only make suggestions for a few build tools, ideally these techniques can be generalized.

## Concurrent Type-Checking

Type-checking typically requires semantic information from other files, and can be relatively expensive compared to other steps like emitting.
Because type-checking can take a little bit longer, it can impact the inner development loop - in other words, you might experience a longer edit/compile/retry cycle, and this might be frustrating.

For this reason, some build tools can run type-checking in a concurrent process without blocking emit.
While this means that invalid code can run before TypeScript reports an error in the tool, you'll often see errors in your editor first, and you won't be blocked for as long from running working code.

An example of this in action is Webpack's `fork-ts-checker` plugin.

## Isolated File Emit

By default, TypeScript's emit requires semantic information that might not be local to a file.
This is to understand how to emit features like `const enum`s and `namespace`s.
But needing to check other files to emit of an arbitrary file can make emit slower.

The need for features that need non-local information is somewhat rare - regular `enum`s can be used in place of `const enum`s, and modules can be used instead of `namespace`s.
For that reason, TypeScript provides the `isolatedModules` flag to warn when using them.
Using `isolatedModules` means that your codebase is safe for tools to use TypeScript APIs like `transpileModule` or alternative compilers like Babel.

As an example of this in action, ts-loader provides

* the `transpileOnly` flag to use `transpileModule`.
* the `useBabel` flag

<!-- TODO -->

# Investigating Issues

There are certain ways to get hints of what might be going wrong.

## `extendedDiagnostics`

You can run TypeScript with `--extendedDiagnostics` to get a printout of where the compiler is spending its time.

```
Files:                         6
Lines:                     24906
Nodes:                    112200
Identifiers:               41097
Symbols:                   27972
Types:                      8298
Memory used:              77984K
Assignability cache size:  33123
Identity cache size:           2
Subtype cache size:            0
I/O Read time:             0.01s
Parse time:                0.44s
Program time:              0.45s
Bind time:                 0.21s
Check time:                1.07s
transformTime time:        0.01s
commentTime time:          0.00s
I/O Write time:            0.00s
printTime time:            0.01s
Emit time:                 0.01s
Total time:                1.75s
```

The most relevant information for most users is:

Field   | Meaning
--------|---------
`Files` | the number of files that the program is including (use `listFiles` to see what they are).
`I/O Read time` | time spent reading from the file system - this includes traversing `include`'d folders.
`Parse time` | time spent scanning and parsing the program
`Program time` | combined time spent performing reading from the file system, scanning and parsing the program, and other calculation of the program graph. These steps are intermingled and combined here because files need to be resolved and loaded once they're included via `import`s and `export`s.
`Bind time` | Time spent building up various semantic information that is local to a single file.
`Check time` | Time spent type-checking the program.
`transformTime time` | Time spent rewriting TypeScript ASTs (trees that represent source files) into forms that work in older runtimes.
`commentTime` | Time spent calculating comments in output files.
`I/O Write time` | Time spent writing/updating files on disk.
`printTime` | Time spent calculating the string representation of an output file.

<!-- TODO: is this important? Looks identical to printTime

`Emit time` | ...

-->

## `showConfig`

It's not always obvious what settings a compilation is being run with when running `tsc`, especially given that `tsconfig.json`s can extend other configuration files.
`showConfig` can explain what `tsc` will calculate for an invocation.

```sh
tsc --showConfig

# or to select a specific config file...

tsc --showConfig -p tsconfig.json
```

## `traceResolution`

Running with `traceResolution` can help explain *why* a file was included in a compilation.
The emit is somewhat verbose, so you might want to redirect output to a file.

```sh
tsc -p tsconfig.json > resolution.txt
```

## Running `tsc` Alone

Much of the time, users run into slow performance using 3rd party build tools like Gulp, Rollup, Webpack, etc.
Running with `tsc --extendedDiagnostics` to find major discrepancies between using TypeScript and the tool can indicate external misconfiguration or inefficiencies.

Some questions to keep in mind:

* Is there a major difference in build times between `tsc` and the build tool with TypeScript integration?
* If the build tool provides diagnostics, is there a difference between TypeScript's resolution and the build tool's?
* Does the build tool have *its own configuration* that could be the cause?
* Does the build tool have configuration *for its TypeScript integration* that could be the cause? (e.g. options for ts-loader?)

## Disabling Editor Plugins

Editor experiences can be impacted by plugins.
Try disabling plugins (namely, JavaScript/TypeScript-related plugins) to see if that fixes any issues in performance and responsiveness.

Certain editors also have their own troubleshooting guides for performance, so consider reading up on them.
For example, Visual Studio Code has its own page for [Performance Issues](https://github.com/microsoft/vscode/wiki/Performance-Issues) as well.

## Upgrading Dependencies

Sometimes TypeScript's type-checking can be impacted by computationally intensive `.d.ts` files.
This is rare, but can happen.
Upgrading to a newer version of TypeScript (which can be more efficient) or to a newer version of an `@types` package (which may have reverted a regression) can often solve the issue.

# Common Issues

Once you've trouble-shooted, you might want to explore some fixes to common issues.
If the following solutions don't work, it may be worth [filing an issue](https://github.com/microsoft/TypeScript/issues/new/choose).

## Misconfigured `include` and `exclude`

As mentioned above, the `include`/`exclude` options can be misused in several ways.

Problem          | Cause  | Fix
-----------------|--------|-------
`node_modules` was accidentally included from deeper folder | *`exclude` was not set* | `"exclude": ["**/node_modules", "**/.*/"]`
`node_modules` was accidentally included from deeper folder | `"exclude": ["node_modules"]` | `"exclude": ["**/node_modules", "**/.*/"]`
Hidden dot files (e.g. `.git`) were accidentally included | `"exclude": ["**/node_modules"]` | `"exclude": ["**/node_modules", "**/.*/"]`
Unexpected files are being included. | *`include` was not set* | `"include": ["src"]`

<!--

# Explicit File Lists

Sometimes a `tsconfig.json` can be found by TypeScript, but the command line experience doesn't appear to reflect it.
This is often because users specify explicit files to `tsc`, which will stop TypeScript from seeking out a tsconfig.

```sh
# doesn't look for a tsconfig.json!
tsc main.ts helper.ts
```

As a result, users often run into using the default options with warnings like

```
Cannot find module '{0}'.
Accessors are only available when targeting ECMAScript 5 and higher.
```

-->

# Filing an Issue

The best reports of performance issues contain *easily obtainable* and *minimal* reproductions of the problem.
In other words, a codebase that can easily be cloned over git that contains only a few files.
They require either no external integration with build tools - they can either be invoked via `tsc` or use isolated code which consumes the TypeScript API.
Codebases that require complex invocations and setups cannot be prioritized.

We understand that this is not always easy to achieve - specifically, because it is hard to isolate the source of a problem within a codebase, and because sharing intellectual property may be an issue.
In some cases, the team will be willing to send a non-disclosure agreement if we believe the issue is highly impactful.

Aside from that, here are some other things a performance issue report should provide.

* `extendedDiagnostics` output
* 

<!-- TODO -->
