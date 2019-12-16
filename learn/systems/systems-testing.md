### Tests for TypeScript

Yep, TypeScript has tests. Quite a lot, with a few different techniques:

- [[Baselines|systems-baselines]]
- [[Fourslash|systems-fourslash]]

## Commands worth knowing

You run tests via `gulp runtests`.

Flags worth knowing:

- `--failed` - re-runs the failed tests only
- `--no-lint` - don't run the linter when it completes
- `-i` - Use the inspector to debug

If you have a fail in the baselines:

- `gulp diff` - to see the differences
- `gulp baseline-accept` to overwrite the current baseline snapshots
