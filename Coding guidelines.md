## Names
1. Use PascalCase for type names
2. Do not use "I" as a prefix for interface names
3. Use PascalCase for enum values
4. Use camelCase for function names
5. Use camelCase for property names and local variables
6. Do not use "_" as a prefix for private properties

## Components 
1. 1 file per logical component (e.g. parser, scanner, emitter, checker)
2. Do not add new files :)
3. files with ".generated" suffix are auto-generated, do not hand edit them

## Types
1. Do not export types/functions unless you need to share it across multiple components
2. Do not introduce new types/values to the global namespace
3. Shared types should be defined in 'types.ts'
4. Within a file, type definitions should come first

## Null and undefined:
1. Use **undefined**, do not use null

## General Assumptions
1. Consider objects like Nodes, Symbols, etc.. as immutable outside the component that created them. Do not change them
2. Consider arrays as immutable by default

## Classes
1. For consistency Do not use classes in the core compiler pipeline. Use function closure instead
	
## Flags
1. More than 2 Boolean properties on a type should be turned into flags

## Comments
1. Use JSDoc style comments

## Strings
1. Use double quotes for strings
2. All strings visible to the user need to be localized (have an entry in diagnosticMessages.json)

## Diagnostic Messages
1. Use a period at the end of a sentence
2. Use indefinite articles for indefinite entities
3. Definite entities should be named (this is for a variable name, type name, etc..)
4. When stating a rule, the subject should be in the singular (e.g. "An external module cannot..." instead of "External modules cannot...")
5. Use present tense

## Style
<TBA>