## Tooling which runs  microsoft/TypeScript

The TypeScript compiler repo has a few different automation tools running on it. 
This document is a rough map of what is running and where you can find the code.

### @typescript-bot

The bot which handles commands in GitHub comments like:

```
@typescript-bot pack this
```

Is fully documented on [the wiki here](https://github.com/microsoft/TypeScript/wiki/Triggering-TypeScript-Bot) and the source code live at [`weswigham/typescript-bot-test-triggerer`](https://github.com/weswigham/typescript-bot-test-triggerer) .

### Label Bots

The bot which adds/removes labels and assignees lives at [microsoft/TypeScript-repos-automation](https://github.com/microsoft/TypeScript-repos-automation) and on [Azure here](https://ms.portal.azure.com/#@microsoft.onmicrosoft.com/resource/subscriptions/57bfeeed-c34a-4ffd-a06b-ccff27ac91b8/resourceGroups/typescriptreposautomatio/providers/Microsoft.Web/sites/TypeScriptReposAutomation).

### Repros

A scheduled task which evaluates code samples generated in [the Bug Workbench](https://www.typescriptlang.org/dev/bug-workbench).

This automation runs via a [daily GitHub Action](https://github.com/microsoft/TypeScript/blob/master/.github/workflows/twoslash-repros.yaml) where the majority of the code lives at [`microsoft/TypeScript-Twoslash-Repro-Action`](https://github.com/microsoft/TypeScript-Twoslash-Repro-Action)

### Other webhooks

We've got a few out-going webhooks:

- We send PRs Issues and Pushes to [Gitter](https://gitter.im/Microsoft/TypeScript)
- We send PRs Issues and Pushes to [Discord](https://discord.gg/TypeScript)

Which is mainly just to provide a high level insight for community members.

Projects which I could not find a source or Azure link for:

- fabricbot-typescript 
- trialgithubcommentresponder
