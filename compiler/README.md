### Getting Started with the Compiler

Yep, the compiler can be a bit of an overwhelming project to understand from scratch, however there are some great
places to start learning.

You should run this in the background while you explore the wiki:

```sh
# clone
git clone https://github.com/microsoft/TypeScript
cd TypeScript

# setup dependencies and build
npm i
gulp

# open up your editor
code .

# run tests to validate your install
gulp runtests-parallel
```

To dive deeper into getting set up to work on the codebase, read [the CONTRIBUTING.md](./CONTRIBUTING.md).

### Critical prior knowledge

1. Read the [Architecture Document](https://github.com/microsoft/TypeScript/wiki/Architectural-Overview) to understand the pieces
1. Read the [Compiler API Examples](https://github.com/microsoft/TypeScript/wiki/Using-the-Compiler-API) to see some small scoped examples of the compiler in action

### Useful to know exists

1. [A glossary of terms](./GLOSSARY.md)
1. The [codebase](./codebase) and [systems](./systems) folders contain overviews of different parts of the compiler
1. The [section on testing](./systems/testing)

### Talks and other content

- [Basarat's Compiler Internals](https://basarat.gitbooks.io/typescript/content/docs/compiler/overview.html)
- The TypeScript compiler team review JSDoc Pull Requests: [part 1](https://www.youtube.com/watch?v=3vwO4DwlGYE), [part 2](https://www.youtube.com/watch?v=Xq4p5LXaO8Y)
