> For a overview of the general TypeScript compiler architecture and layering, see [[Architectural Overview]]

## Overview

A simple analogy for a language service object is a long-lived program, or the compilation context. 

## Design Goals

There are two main goals that the language service design tries to achieve:

### On demand processing
The language service is designed to allow quick response that scales with the size of the program. The only way this can be achieved is only doing the absolute minimum work required. All language service interfaces only compute the needed level of information needed to answer the current query. For instance, a call to getSyntaxDiagnostics will only need the file in question to be parsed, but not type checked. A call getCompletionsAtPosition will only attempt to resolve declarations contributing to the type in question but not others.

### Decoupling compiler pipeline phases
The language service design decouples different phases of the compiler pipeline, that would normally happen in order in one shot during command-line compilation; and it allows the language service host flexibility in ordering these different phases. 

For instance the language service reports diagnostics on a file per file bases, and for syntax errors of files separately from semantic errors, and form general options and resolution errors. This allows the host to create an optimized experience of getting syntax errors for the current file being edited quickly, without having to pay the cost of querying other files, or doing a full semantic check. It also allows the host to skip querying for syntax errors for files that have not changed. Similarly, the language service allows for emitting a file (getEmitOutput) without having to emit or even type check the whole program.

## Language Service Host

The host is descried by the LanguageServiceHost API, and it abstracts all interactions between the language service and the external world. The language service host defers managing, monitoring and maintaining input files to the host.

The language service will only ask the host for information as part of host calls. No asynchronous events, or background processing are expected. The host is expected to manage threading if needed.

The host is expected to supply the full set of files compromising the context. for resolving references refer to: 


## Reference resolution in the language service
There are two two means of declaring dependencies in TypeScript, import statements, and triple-slash reference comments (/// <reference path="" />). Reference resolution for a program is the process of walking the dependency graph between files, and generating a sorted list of files compromising the program. 

In the command-line compiler (tsc) this happens as part of building the program. A createProgram call starts with a set of root files, parse them in order, and walk their dependency declaration (imports and triple-slash references) resolving references to actual files on disk and then pulling them into the compilation process.

This work flow is decoupled in the language service into two phases, allowing the host to interject at any point and change the resolution logic if needed. It also allows the host to fully manage program structure and optimize file state change.

To resolve references originating from a file, use `ts.preProcessFile`. This method will resolve both imports and triple-slash references from this specific files. Also worth noting that this relies on the scanner, and does not require a full parse to allow for fast context resolution, suited to editor interactions.

## Document Registry
