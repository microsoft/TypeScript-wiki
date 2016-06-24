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

The parser is a recursive descent parser.
It's pretty resilient to small changes, so if you search for function names matching the thing you want to change, you can probably get away with not having to think about the whole parser.
There aren't any surprises in the general implementation style here.

## Incremental parsing

## ECMAScript parsing contexts

## ECMAScript automatic semicolon insertion

## JSDoc parsing

# Binder

# Checker

The checker is almost 20,000 lines long, and does almost everything that's not syntactic -- it's the second of two semantic passes, after binding, which is the first semantic pass.
Since the semantics of a entire program can change dramatically with a couple of keystrokes (e.g. renaming a class), a new checker gets created every time the language service requests information.
Creating a checker is cheap, though, because the compiler as a whole is so lazy.
You just have to create some basic types and get the binder to build the global symbol table.

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
