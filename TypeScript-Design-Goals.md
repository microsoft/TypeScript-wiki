# Introduction
This document serves to outline the general design principles we have based the TypeScript language on. While it is by no means exhaustive, it aims to summarize the rules by which we've made many of the decisions that have shaped the language. Some of these rules are subjective, and at times are at odds with each other; reaching the right balance and making the right exceptions is the essence of how successful programming languages are designed.

We recommend reading Chapter 1 of the TypeScript Language Specification for more background on the overall aims of the TypeScript language itself, and how it intends those goals to be achieved.

# Goals
 1. Statically identify constructs that are likely to be errors.
 1. Provide a structuring mechanism for larger pieces of code.
 1. Impose no runtime overhead on emitted programs.
 1. Emit clean, idiomatic, recognizable JavaScript code.
 1. Produce a language that is composable and easy to reason about.
 1. Align with current and future ECMAScript proposals.
 1. Preserve runtime behavior of all JavaScript code.
 1. Avoid adding expression-level syntax.
 1. Use a consistent, fully erasable, structural type system.
 1. Be a cross-platform development tool.

# Non-goals
 1. Exactly mimic the design of existing languages. Instead, use the behavior of JavaScript and the intentions of program authors as a guide for what makes the most sense in the language.
 1. Aggressively optimize the runtime performance of programs. Instead, emit idiomatic JavaScript code that plays well with the performance characteristics of runtime platforms.
 1. Apply a sound or "provably correct" type system. Instead, strike a balance between correctness and productivity.
 1. Provide an end-to-end build pipeline. Instead, make the system extensible so that external tools can use the compiler for more complex build workflows.
 1. Add or rely on run-time type information in programs. Instead, encourage programming patterns that do not require run-time metadata.
 1. Provide additional runtime functionality or libraries. Instead, use TypeScript to describe existing libraries.
