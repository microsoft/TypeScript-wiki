# Binder

The binder walks the tree visiting each declaration in the tree.
For each declaration that it finds, it creates a `Symbol` that records its location and kind of declaration.
Then it stores that symbol in a `SymbolTable` in the containing node, like a function, block or module file, that is the current scope.
`Symbol`s let the checker look up names and then check their declarations to determine types.
It also contains a small summary of what kind of declaration it is -- mainly whether it is a value, a type, or a namespace.

Since the binder is the first tree walk before checking, it also does some other tasks: setting up the control flow graph,
as well as annotating parts of the tree that will need to be downlevelled for old ES targets.

Here's an example:

```ts
// @Filename: main.ts
var x = 1
console.log(x)
```

The only declaration in this program is `var x`, which is contained in the SourceFile node for `main.ts`.
Functions and classes introduce new scopes, so they are containers -- at the same time as being declarations themselves. So in:

```ts
function f(n: number) {
    const m = n + 1
    return m + n
}
```

The binder ends up with a symbol table for `f` that contains two entries: `n` and `m`.
The binder finds `n` while walking the function's parameter list, and it finds `m` while walking the block that makes up `f`'s body.

Both `n` and `m` are marked as values.
However, there's no problem with adding another declaration for `n`:

```ts
function f(n: number) {
    type n = string
    const m = n + 1
    return m + n
}
```

Now `n` has two declarations, one type and one value.
The binder disallows more than one declaration of a kind of symbols with *block-scoped* declaration.
Examples are `type`, `function`, `class`, `let`, `const` and parameters; *function-scoped* declarations include `var` and `interface`.
But as long as the declarations are of different kinds, they're fine.

## Walkthrough

```ts
function f(m: number) {
    type n = string
    const n = m + 1
    return m + n
}
```

The binder's basic tree walk starts in `bind`.
There, it first encounters `f` and calls `bindFunctionDeclaration` and then `bindBlockScopeDeclaration` with `SymbolFlags.Function`.
This function has special cases for files and modules, but the default case calls `declareSymbol` to add a symbol in the current container.
There is a lot of special-case code in `declareSymbol`, but the important path is to check whether the symbol table already contains a symbol with the name of the declaration -- `f` in this case.
If not, a new symbol is created.
If so, the old symbol's exclude flags are checked against the new symbol's flags.
If they conflict, the binder issues an error.

Finally, the new symbol's `flags` are added to the old symbol's `flags` (if any), and the new declaration is added to the symbol's `declarations` array.
In addition, if the new declaration is for a value, it is set as the symbol's `valueDeclaration`.

## Containers

After `declareSymbol` is done, the `bind` visits the children of `f`; `f` is a container, so it calls `bindContainer` before `bindChildren`.
The binder is recursive, so it pushes `f` as the new container by copying it to a local variable before walking its children.
It pops `f` by copying the stored local back into `container`.

The binder tracks the current lexical container as a pair of variables `container` and `blockScopedContainer` (and `thisParentContainer` if you OOP by mistake).
It's implemented as a global variable managed by the binder walk, which pushes and pops containers as needed.
The container's symbol table is initialised lazily, by `bindBlockScopedDeclaration`, for example.

## Flags

The table for which symbols may merge with each other is complicated, but it's implemented in a surprisingly small space using the bitflag enum `SymbolFlags`.
The downside is that the bitflag system is very confusing.

The basic rule is that a new declaration's *flags* may not conflict with the *excludes flags* of any previous declarations.
Each kind of declaration has its own exclude flags; each one is a list of declaration kinds that cannot merge with that declaration.

In the example above, `type n` is a type alias, which has flags = `SymbolFlags.TypeAlias` and excludeFlags = `SymbolFlags.TypeAliasExcludes`.
The latter is an alias of `SymbolFlags.Type`, meaning generally that type aliases can't merge with anything that declares a type:

```ts
Type = Class | Interface | Enum | EnumMember | TypeLiteral | TypeParameter | TypeAlias
```
Notice that this list includes `TypeAlias` itself, and declarations like classes and enums that also declare values.
`Value` includes `Class` and `Enum` as well.

Next, when the binder reaches `const n`, it uses the flag `BlockScopedVariable` and excludeFlags `BlockScopedVariableExcludes`.
`BlockScopedVariableExcludes = Value`, which is a list of every kind of value declaration.

```ts
Value = Variable | Property | EnumMember | ObjectLiteral | Function | Class | Enum | ValueModule | Method | GetAccessor | SetAccessor
```

`declareSymbol` looks up the existing excludeFlags for `n` and makes sure that `BlockScopedVariable` doesn't conflict; `BlockScopedVariable & Type === 0` so it doesn't.
Then it *or*s the new and old flags and the new and old excludeFlags.
In this example, that will prevent more value declarations because `BlockScopedVariable & (Value | Type) !== 0`.

Here's some half-baked example code which shows off what you'd write if SymbolFlags used string enums and sets instead of bitflags.

```ts
const existing = symbolTable.get(name)
const flags = SymbolFlags[declaration.kind] // eg "Function"
if (existing.excludes.has(flags)) {
  error("Cannot redeclare", name)
}
existing.flags.add(flags)
for (const ex of ExcludeFlags[declaration.kind]) {
  existing.excludeFlags.add(ex)
}
```

## Cross-file global merges

Because the binder only binds one file at a time, the above system for merges only works with single files.
For global (aka script) files, declarations can merge across files.
This happens in the checker in `initializeTypeChecker`, using `mergeSymbolTable`.

## Special names

In `declareSymbol`, `getDeclarationName` translates certain nodes into internal names.
`export=`, for example, gets translated to `InternalSymbolName.ExportEquals`

Elsewhere in the binder, function expressions without names get `"__function"`
Computed property names that aren't literals get `"__computed"`, manually.

TODO: Finish this

## Control Flow

TODO: Missing completely

## Emit flags

TODO: Missing completely

## Exports

TODO: Missing completely

## Javascript and CommonJS

Javascript has additional types of declarations that it recognises, which fall into 3 main categories:

1. Constructor functions and pre-class-field classes: assignments to `this.x` properties.
2. CommonJS: assignments to `module.exports`.
3. Global browser code: assignments to namespace-like object literals.
4. JSDoc declarations: tags like `@type` and `@callback`.

Four! Four main categories!

The first three categories really aren't much different from Typescript declarations.
The main complication is that not all assignments are declarations, so there's quite a bit of code that decides which assignments should be treated as declarations.
The checker is fairly resilient to non-declaration assignments being included, so it's OK if the code isn't perfect.

In `bindWorker`'s `BinaryExpression` case, `getAssignmentDeclarationKind` is used to decide whether an assignment matches the syntactic requirements for declarations.
Then each kind of assignment dispatches to a different binding function.

### Global namespace creation code

In addition to CommonJS, JS also supports creating global namespaces by assignments of object literals, functions and classes to global variables.
This code is very complicated and is *probably* only ever used by Closure code bases, so it might be possible to remove it someday.

``` js
var Namespace = {}
Namespace.Mod1 = {}
Namespace.Mod2 = function () {
  // callable module!
}
Namespace.Mod2.Sub1 = {
  // actual contents
}
```

TODO: This is unfinished.

### JSDoc declarations

TODO: This is unfinished.

### Conflicting object literal export assignments

One particuarly complex case of CommonJS binding occurs when there is an object literal export assignment in the same module as `module.exports` assignments:

```js
module.exports = {
    foo: function() { return 1 },
    bar: function() { return 'bar' },
    baz: 12,
}
if (isWindows) {
    // override 'foo' with Windows-specific version
    module.exports.foo = function () { return 11 }
}
```

In this case, the desired exports of the file are `foo, bar, baz`.
Even though `foo` is declared twice, it should have one export with two declarations.
The type should be `() => number`, though that's the responsibility of the checker.

In fact, this structure is too complicated to build in the binder, so the checker produces it through merges, using the same merge infrastructure it uses for cross-file global merges.
The binder treats this pretty straightforwardly; it calls `bindModuleExportsAssignment` for `module.exports = {...`, which creates a single `export=` export.
Then it calls `bindExportsPropertyAssignment` for `module.exports.foo = ...`, which creates a `foo` export.

Having `export=` with other exports is impossible with ES module syntax, so the checker detects it and copies all the top-level exports into the `export=`.
In the checker, `resolveExternalModuleSymbol` returns either an entire module's exports, or all the exports in an `export=`.
In the combined CommonJS case we're discussing, `getCommonJsExportEquals` also checks whether a module has exports *and* `export=`.
If it does, it copies each of the top-level exports into the `export=`.
If a property with the same name already exists in the `export=`, the two are merged with `mergeSymbol`.

Subsequent code in the checker that doesn't use `resolveExternalModuleSymbol` (is there any?) has to ignore the `export=`, since its contents are now just part of the module.
