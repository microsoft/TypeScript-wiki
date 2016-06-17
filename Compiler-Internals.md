This page details the compiler implementation and its philosophy.
Because it focuses on implementation, it's necessarily out-of-date and incomplete.

Before reading this page, be sure to read the [[Architectural Overview]] first.

# General Design

## Laziness

To support language services that respond interactively, the compiler is lazy: it does not calculate any information until it is required.
This allows it to respond quickly when the language service requests the type of a variable or its members.
Unfortunately, laziness also makes the compiler code more complicated.

As an overview, after parsing is complete, the binder does nothing but identify symbols.
The checker then waits until a particular symbol is requested to calculate type information, etc.

## Immutability

Each phase of the compiler (parser, binder, etc -- see below for details) treats data structures from the previous phases as immutable.
In addition, data structures created within each phase are not usually modified after their creation.
This requires a look-aside table in some cases.
For example, because the binder only looks at one file at a time, the checker needs a merged-symbols table to track merged declarations.
It checks whether a symbol has an entry in the merged-symbols table each time before it uses a symbol.

# Parser

It's a recursive descent parser. It's pretty resilient, so if you search for functions matching the thing you want to change, you can probably get away with just adding the code to parse.
There aren't any surprises in the general implementation style here.

## Incremental parsing

## ECMAScript parsing contexts

## ECMAScript automatic semicolon insertion

## JSDoc parsing

# Checker

The checker is almost 20,000 lines long, and does almost everything that's not syntactic.
Surprisingly, a checker gets created every time the language service requests information because it tries to present an immutable interface.

## Grammatical checking

## Overload resolution

## Type argument inference

### Type argument fixing

# Transformer

The transformer is nearing completion to replace the emitter.
The change in name is because the *emitter* translated TypeScript to JavaScript.
The *transformer* transforms TypeScript or JavaScript (various versions) to JavaScript (various versions) using various module systems.
The input and output are basically both trees from the same AST type, just using different features.
There is still a small printer that writes any AST back to text.

## Rewriting & synthesized nodes

## Sourcemap generation

# Language service

The language service provides an API for clients to provide edit-time information about a program.
Point it at a directory and it will maintain compilation information about the source in that directory.
