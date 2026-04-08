# Security Properties of `tsc`

## Overview

The TypeScript compiler (`tsc`) is a **build tool**, not a sandbox. It transforms TypeScript source files into JavaScript output files. This document describes what `tsc` guarantees and does not guarantee when invoked on untrusted input.

## Security Guarantees

**No arbitrary code execution.** Running `tsc` on a malicious `.ts` or `tsconfig.json` file will never cause the input code to be executed. The compiler parses, type-checks, and emits; it does not evaluate the programs it compiles. There is no `eval`-at-compile-time, no macro system, and no plugin mechanism that runs author-supplied code during compilation. This is the core security property of `tsc`.

**Deterministic side effects.** The only side effect of a successful `tsc` invocation is writing output files (`.js`, `.d.ts`, `.map`, `.tsbuildinfo`) to disk. It does not make network requests, spawn child processes, or interact with the system beyond file I/O.

**Safe exit.** Certain adverserial inputs may cause crashes, but these crashes will unwind the process normally, and will not be a source of buffer overrun or other memory safety exploit vectors.

## Non-Guarantees

**Arbitrary file writes.** `tsc` writes compiler output to paths derived from its configuration (`outDir`, `outFile`, `declarationDir`, etc.) and the structure of the input project. A malicious `tsconfig.json` can direct output to any path writable by the calling user. This is by design: writing files to disk *is the point* of a compiler. Callers who need to constrain output locations must do so externally (e.g., filesystem permissions, containers, sandboxing). Similarly, running `tsc --build --clean` may delete files from disk; crafted `.tsbuildinfo` or `tsconfig.json`s may cause any file to be deleted.

**Resource consumption.** TypeScript's type system is Turing-complete. A crafted input file can cause `tsc` to consume unbounded CPU time or memory during type-checking. The compiler provides no built-in timeouts or memory limits. Callers operating on untrusted input should enforce resource limits externally (e.g., `ulimit`, cgroups, process timeouts). You should not assume that an adverserially-constructed program will successfully typecheck in any bounded amount of time.

**Crash safety.** `tsc` may gracefully crash, hang, or produce unexpected diagnostics when given adversarial input. While most crashes are treated as bugs and fixed when reported, the compiler does *not* guarantee graceful handling of all possible malformed inputs (e.g. an unbounded series of `f(f(f(f(...`).

## Summary

| Property | Guaranteed? |
|---|---|
| No execution of input code | ✅ Yes |
| Side effects limited to file writes | ✅ Yes |
| Output written only to expected paths | ❌ No — controlled by config |
| Bounded time and memory | ❌ No — type system is Turing-complete |
| No crashes on adversarial input | ❌ No |

**In short:** `tsc` is safe to run on untrusted code in the sense that it will only read and write files, and never execute the code it compiles or other arbitrary code. All other resource and path constraints are the caller's responsibility.
