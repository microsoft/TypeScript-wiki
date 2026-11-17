# Security Properties of `tsc`

## Overview

The TypeScript compiler (`tsc`) is a **build tool**, not a sandbox. It transforms TypeScript source files into JavaScript output files. This document describes what `tsc` guarantees and does not guarantee when invoked on untrusted input.

## Security Guarantees

**No arbitrary code execution.** Running `tsc` on a malicious `.ts` or `tsconfig.json` file will never cause the input code to be executed. The compiler parses, type-checks, and emits; it does not evaluate the programs it compiles. There is no `eval`-at-compile-time, no macro system, and no plugin mechanism that runs author-supplied code during compilation. This is the core security property of `tsc`.

*Exception*: If content mappers are enabled, this *does* enable execution of third-party code. Only pass this flag if you have validated which content mappers are available and that you are OK with running them.

**Deterministic side effects.** The only side effect of a successful `tsc` invocation is writing output files (`.js`, `.d.ts`, `.map`, `.tsbuildinfo`) to disk. It does not make network requests, spawn child processes, or interact with the system beyond file I/O.

**Safe exit.** Certain adversarial inputs may cause crashes, but these crashes will unwind the process normally, and will not be a source of buffer overrun or other memory safety exploit vectors.

## Non-Guarantees

**Arbitrary file writes.** `tsc` writes compiler output to paths derived from its configuration (`outDir`, `outFile`, `declarationDir`, etc.) and the structure of the input project. A malicious `tsconfig.json` can direct output to any path writable by the calling user. This is by design: writing files to disk *is the point* of a compiler. Callers who need to constrain output locations must do so externally (e.g., filesystem permissions, containers, sandboxing). Similarly, running `tsc --build --clean` may delete files from disk; crafted `.tsbuildinfo` or `tsconfig.json`s may cause any file to be deleted.

**File read sandboxing.** `tsc` will read files from paths specified in input files, and transitive references from there, including import paths and reference directives. An attacker in control of these files may therefore cause a *read* of any file path the `tsc` process has privileges to read, and `tsc` may reprint certain file contents in its output messages. In other words, for example, it is not generally safe to run `tsc` on untrusted code and print back the error message contents to an untrusted party, as this could expose local filesystem contents to the attacker. Use of standard sandboxing strategies is recommended to secure scenarios similar to this.

**Resource consumption.** TypeScript's type system is Turing-complete. A crafted input file can cause `tsc` to consume unbounded CPU time or memory during type-checking. The compiler provides no built-in timeouts or memory limits. Callers operating on untrusted input should enforce resource limits externally (e.g., `ulimit`, cgroups, process timeouts). You should not assume that an adverserially-constructed program will successfully typecheck in any bounded amount of time.

**Crash safety.** `tsc` may gracefully crash, hang, or produce unexpected diagnostics when given adversarial input. While most crashes are treated as bugs and fixed when reported, the compiler does *not* guarantee graceful handling of all possible malformed inputs (e.g. an unbounded series of `f(f(f(f(...`).

## Language Service

The TypeScript Language Service (LS) only executes in the context of a [trusted workspace (VS Code)](https://code.visualstudio.com/docs/editing/workspaces/workspace-trust) or [trusted folder (VS)](https://learn.microsoft.com/en-us/visualstudio/ide/trust-settings?view=visualstudio). Similar to tsc, there are no guaranteed resource caps in the LS, and "hangs" may occur in the presence of adversarial inputs.

## Summary

| Property | Guaranteed? |
|---|---|
| No execution of input code | ✅ Yes |
| Side effects limited to file writes | ✅ Yes |
| Output written only to expected paths | ❌ No — controlled by config |
| Bounded time and memory | ❌ No — type system is Turing-complete |
| No crashes on adversarial input | ❌ No |

**In short:** `tsc` is safe to run on untrusted code in the sense that it will only read and write files, and never execute the code it compiles or other arbitrary code. All other resource and path constraints are the caller's responsibility.

# Security Invariants in the TypeScript repo

Maintainers may trigger certain automated jobs through actions such as approving workflows to run CI or issuing commands to `typescript-bot`/`typescript-automation`. **This is a security boundary**: these actions necessarily execute code that is part of a PR, and a security boundary is that maintainers should not invoke these commands without first checking the content of the PR. It is not a security issue for code execution to occur as a result of a maintainer action; this is basically "the same as" the maintainer running the code on their machine locally.

Certain invariants are still security issues if violated. For example:
 * Timing attacks: a malicious PR author could push an additional commit after a maintainer triggered an automated activity. It is a security violation if the automation does not correctly associate an automated activity invocation with the head commit of the PR at the time the command was issued.
 * Token exfiltration: most CI jobs use tokenless auth flows, but any tokens that are potentially exfiltratable will not have permissions above what the PR author would have equal (or greater) access to with their own account. It would be a security issue if this token had additional privileges beyond what the attacker already has implicit access to by virtue of having a GitHub account in the first place.


