We do not add new "utility" types to the standard library.

The existing global utility types you see (`Omit`, `Pick`, etc) are either required for declaration emit, or were written before this rule was put into place.

The primary reason for this is that, for a given type, we need to:
 * Define its overall desired behavior
 * Pick a name that doesn't conflict with anything likely to be in userspace, but is still descriptive
 * Decide if they are distributive or not distributive
 * Decide if they should be strongly or weakly defined in terms of their constraints
 * Make many other type-specific decisions

Reasonable people can disagree about any of these points, much as they do about other design decisions. For example, [the typing of `Array#includes` is "stricter" than some people would like](https://github.com/microsoft/TypeScript/issues/26255), whereas `Omit` is not [considered "strict enough" by some people](https://github.com/microsoft/TypeScript/issues/30825). Any time we've introduced a utility type, it's spurred these disatisfications where people feel that TypeScript obviously didn't just pick the "correct" definition, even though the definition we added matched someone else's "correct" definition. Even types you might think are easy to define, like `Nullable<T>`, can and do get [different definitions by different people](https://github.com/microsoft/TypeScript/pull/51254/files). And in the event we change our minds and agree about which definition *should* have been used, it's too much of a breaking change to modify these types at all once they're in common use.

Since there's no *need* for these utility types, and people rightfully are annoyed that any definition we pick will effectively prevent anyone else from using those names (due to confusion), our current stance is that it's better to leave utility type definition up to users so that they can pick the definitions that best match their individual desired semantics, rather than introducing a new global type that almost will certainly raise justifiable ire among a nontrivial set of users.