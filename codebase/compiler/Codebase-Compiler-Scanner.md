# Scanner

One of the smallest parts of the compiler's critical path to AST then type-checking. It exists to create a stream
of syntax tokens for use by another object. The scanner gets most of its usage via a [parser][0] instance.

## Overview

You create a Scanner instance via [`createScanner`][1], there are two main modes of scanning: Standard and JSX.
Then there are specific functions for scanning inside JSDoc comments.

First you set the text of a scanner to be your JS source code via [`setText`][2], this takes the source code and
an optional start and end range in which you want to scan. This range is an important part of what keeps
TypeScript's server mode fast via [incremental parsing][3].

At a high level, the scanner works by a having another object call [`scanner.scan`][4] this:

- Pulls out the character at the current position (`pos`) in the text
- Runs a very big switch statement against known characters which start syntax
- Then move look forwards till the end of that list of chars to decode if it's what we think it is. here's an
  example of what happens when [the character found][5] is a `!`:

  ```ts
  case CharacterCodes.exclamation:
    // Look to see if it's "!="
    if (text.charCodeAt(pos + 1) === CharacterCodes.equals) {
        // Also check if it's "!=="
        if (text.charCodeAt(pos + 2) === CharacterCodes.equals) {
            // for !== move the position forwards to the end of the symbol
            // then set the token
            return pos += 3, token = SyntaxKind.ExclamationEqualsEqualsToken;
        }
        // Move it two, set the token
        return pos += 2, token = SyntaxKind.ExclamationEqualsToken;
    }

    // Move forwards one character and set the token
    pos++;
    return token = SyntaxKind.ExclamationToken;
  ```

- A `SyntaxKind` is returned from [`scan`][4] and it's up to the scanner owner to do work with those tokens. The
  scanner keeps track of a few useful values for that:

  - `getStartPos` - where the token was started including preceding whitespace
  - `getTextPos` - the end position of the current token
  - `getTokenText` - the text between the token start and end
  - `getTokenValue` - some syntax contains a value which can be represented as a string, a good example is
    literally a string. The `"` or `'` are not included in the value.


## Full Start/Token Start

Tokens themselves have what we call a "full start" and a "token start". The "token start" is the more natural version, which is the position in the file where the text of a token begins. The "full start" is the point at which the scanner began scanning since the last significant token. When concerned with trivia, we are often more concerned with the full start.

Function | Description
---------|------------
`ts.Node.getStart` | Gets the position in text where the first token of a node started.
`ts.Node.getFullStart` | Gets the position of the "full start" of the first token owned by the node.


## Trivia

When creating a scanner you get to choose whether whitespace should be returned in the stream of tokens. This is
nearly always off, but it is used inside the [formatter][6] and for syntax highlighting via the TSServer via a
[classifier][7].

Syntax trivia represent the parts of the source text that are largely insignificant for normal understanding of the code, such as whitespace, comments, and even conflict markers.

Because trivia are not part of the normal language syntax (barring ECMAScript ASI rules) and can appear anywhere between any two tokens, they are not included in the syntax tree. Yet, because they are important when implementing a feature like refactoring and to maintain full fidelity with the source text, they are still accessible through our APIs on demand.

Because the `EndOfFileToken` can have nothing following it (neither token nor trivia), all trivia naturally precedes some non-trivia token, and resides between that token's "full start" and the "token start"

It is a convenient notion to state that a comment "belongs" to a `Node` in a more natural manner though. For instance, it might be visually clear that the `genie` function declaration owns the last two comments in the following example:

```TypeScript
var x = 10; // This is x.

/**
 * Postcondition: Grants all three wishes.
 */
function genie([wish1, wish2, wish3]: [Wish, Wish, Wish]) {
    while (true) {
    }
} // End function
```

This is despite the fact that the function declaration's full start occurs directly after `var x = 10;`.

We follow [Roslyn's notion of trivia ownership](https://github.com/dotnet/roslyn/wiki/Roslyn%20Overview#syntax-trivia) for comment ownership. In general, a token owns any trivia after it on the same line up to the next token. Any comment after that line is associated with the following token. The first token in the source file gets all the initial trivia, and the last sequence of trivia in the file is tacked onto the end-of-file token, which otherwise has zero width.

For most basic uses, comments are the "interesting" trivia. The comments that belong to a Node which can be fetched through the following functions:

Function | Description
---------|------------
`ts.getLeadingCommentRanges` | Given the source text and position within that text, returns ranges of comments between the first line break following the given position and the token itself (probably most useful with `ts.Node.getFullStart`).
`ts.getTrailingCommentRanges` | Given the source text and position within that text, returns ranges of comments until the first line break following the given position (probably most useful with `ts.Node.getEnd`).

As an example, imagine this portion of a source file:

```TypeScript
debugger;/*hello*/     
    //bye
  /*hi*/    function
```

The full start for the `function` keyword begins at the `/*hello*/` comment, but `getLeadingCommentRanges` will only return the last 2 comments:

```
d e b u g g e r ; / * h e l l o * / _ _ _ _ _ [CR] [NL] _ _ _ _ / / b y e [CR] [NL] _ _ / * h i * / _ _ _ _ f u n c t i o n 
                  ↑                                     ↑       ↑                       ↑                   ↑
                  full start                       look for     first comment           second comment      token start
                                              leading comments 
                                               starting here
```

Appropriately, calling `getTrailingCommentRanges` on the end of the debugger statement will extract the `/*hello*/` comment.

In the event that you are concerned with richer information of the token stream, `createScanner` also has a `skipTrivia` flag which you can set to `false`, and use `setText`/`setTextPos` to scan at different points in a file.


## JSX

Some of the more complicated aspects of JSX support is mostly handled back in [the parser][0], however JSX support
in the scanner [uses specific syntax tokens][8].

## Flags

One way for the scanner to keep track of scan issues, or internal state is [via `TokenFlags`][9]. Any example of
this is in scanning a number. TypeScript supports underscores in numbers `100_000`, when scanning a number literal
if it detects a `CharacterCodes._` then the flag `TokenFlags.ContainsSeparator` is set and later on that is used
to ensure the `tokenValue` is set correctly.

## Rescanning

Because the scanner is only interested in passing out tokens as it sees them, it doesn't really have a memory of
previous tokens. This means that occasionally the controlling object will need to rewind and re-run the scanner
with a different type of context. This is called rescanning.

## Example code

[Here's a scanner playground](https://5d39df23407c626e65aee7ef--ts-scanner-tokens.netlify.com) - adding TypeScript
will show you the tokens generated by a single scanner. It's worth noting that this doesn't represent that
_actual_ results of the scanner when using TypeScript, because the parser controls re-scanning and this playground
doesn't do that.

<!-- prettier-ignore-start -->
[0]: ./parser.md
[1]: https://github.com/microsoft/TypeScript/blob/db9e0079/src/compiler/scanner.ts#L929
[1]: <src/compiler/scanner.ts - export function createScanner>
[2]: https://github.com/microsoft/TypeScript/blob/db9e0079/src/compiler/scanner.ts#L2551
[2]: <src/compiler/scanner.ts - function setText(>
[3]: GLOSSARY.md#incremental-parsing
[4]: https://github.com/microsoft/TypeScript/blob/db9e0079/src/compiler/scanner.ts#L1609
[4]: <src/compiler/scanner.ts - function scan(>
[5]: https://github.com/microsoft/TypeScript/blob/db9e0079/src/compiler/scanner.ts#L1681
[5]: <src/compiler/scanner.ts - case CharacterCodes.exclamation>
[6]: ./formatter.md
[7]: https://github.com/microsoft/TypeScript/blob/db9e0079/src/services/classifier.ts#L3
[7]: <src/services/classifier.ts - function createClassifier>
[8]: https://github.com/microsoft/TypeScript/blob/db9e0079/src/compiler/types.ts#L709
[8]: <src/compiler/types.ts - type JsxTokenSyntaxKind>
[9]: https://github.com/microsoft/TypeScript/blob/db9e0079/src/compiler/types.ts#L2159
[9]: <src/compiler/types.ts - export const enum TokenFlags>
<!-- prettier-ignore-end -->
