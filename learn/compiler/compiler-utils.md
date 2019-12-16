### Util Functions

Some essentials:

- [`findAncestor`][0]

> Iterates through the parent chain of a node and performs the callback on each parent until the callback returns
> a truthy value, then returns that value.
>
> If no such value is found, it applies the callback until the parent pointer is undefined or the callback returns
> "quit" At that point findAncestor returns undefined.

Basically looks up the AST until it finds something which passes.
