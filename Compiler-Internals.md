This is an incomplete page that will need to be updated as time goes on. The intent is that there are gaps of knowledge that are understood well among the team (or even just specific team members) which would be better if understood across a broader audience.

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

## Incremental parsing

## ECMAScript parsing contexts

## ECMAScript automatic semicolon insertion

# Checker

## Grammatical checking

## Overload resolution

## Type argument inference

### Type argument fixing

# Emitter

## Rewriting & synthesized nodes

## Sourcemap generation