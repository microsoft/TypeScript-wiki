# Intro

These changes list where implementation differs between versions as the spec and compiler are simplified and inconsistencies are corrected.

# Changes between 1.0 and 1.1

## Working with null and undefined in ways that are observably incorrect is now an error

Examples: 

```TypeScript
var ResultIsNumber17 = +(null + undefined);
// Operator '+' cannot be applied to types 'undefined' and 'undefined'.

var ResultIsNumber18 = +(null + null);
// Operator '+' cannot be applied to types 'null' and 'null'.

var ResultIsNumber19 = +(undefined + undefined);
// Operator '+' cannot be applied to types 'undefined' and 'undefined'.
```

Similarly, using null and undefined directly as objects that have methods now is an error

Examples:

```TypeScript
null.toBAZ();

undefined.toBAZ();
```





 