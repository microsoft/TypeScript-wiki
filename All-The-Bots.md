This is a list of the services that post as typescript-bot.

## TypeScript repo

- [GitHub Actions - close issues](https://github.com/microsoft/TypeScript/blob/main/.github/workflows/close-issues.yml)
- [GitHub Actions - PR replies for modified files](https://github.com/microsoft/TypeScript/blob/main/.github/workflows/pr-modified-files.yml)
- https://github.com/microsoft/typescript-bot-test-triggerer -- runs on the Azure Function typescriptbot-github, see [[Triggering TypeScript Bot]] -- responds to "test this" messages from team members.
- https://github.com/microsoft/Typescript-repos-automation -- runs on the Azure Function TypeScriptReposAutomation -- more simple reactions to labels.
- https://github.com/microsoft/TypeScript-Twoslash-Repro-Action -- run `tsrepro` and bisects: https://github.com/microsoft/TypeScript/blob/main/.github/workflows/twoslash-repros.yaml
- https://github.com/microsoft/typescript-error-deltas -- produces PR comments showing new errors caused by PRs
- https://github.com/microsoft/typescript-benchmarking -- produces PR comments showing benchmark results

## Definitely Typed repo

- https://github.com/DefinitelyTyped/dt-mergebot -- runs on the Azure Function DTMergebot -- posts status comments, adds labels, maintains board, merges PRs.
- https://github.com/microsoft/DefinitelyTyped-tools -- runs the publisher on GitHub Actions
- [DangerBotOSS](https://github.com/definitelytyped/definitelytyped/tree/main/.github/workflows/CI.yml) -- suggests missed exports (posts as DangerBotOSS, not typescript-bot)
