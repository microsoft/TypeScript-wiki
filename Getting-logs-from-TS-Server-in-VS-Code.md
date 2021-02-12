The TS Server is a Node process which editors talk to in order to provide rich functionality for your TypeScript/JavaScript code (e.g. auto-completions, go-to-definition, quick fixes, refactorings).

This page describes how to get a TS Server log within Visual Studio Code. You might need this to diagnose why a crash is taking place (often forcing you to restart VS Code or the JS/TS language server), or to make sure that the communication between TypeScript the language and your editor is what you expect.

1. Open the project you want to investigate in Visual Studio Code.
1. Run the command `Open TS Server Log` (You can run commands with `View` > `Command Palette`).
   1. This command should offer to turn on logging if you don't have it enabled. Enable logging if it isn't already.
1. Restart your project, and try to only do the reproduction case.
1. Run `Open TS Server Log` again and it will re-direct you to a file with the logs.

The log should start like:

```
Info 0    [13:5:26.815] Starting TS Server
Info 1    [13:5:26.816] Version: 3.7.0-dev.20190922
Info 2    [13:5:26.816] Arguments: /Applications/Visual Studio Code - Insiders.app/Contents/Frameworks/Code - Insiders Helper.app/Contents/MacOS/Code - Insiders Helper /Users/ortatherox/dev/typescript/TypeScript/node_modules/typescript/lib/tsserver.js --useInferredProjectPerProjectRoot --enableTelemetry --cancellationPipeName /var/folders/3d/j0zt8n5d77n_4mthj6nsb6h00000gn/T/vscode-typescript501/58d39090e29f4276f14f/tscancellation-e9842eb530b8e993176a.tmp* --logVerbosity verbose --logFile /Users/ortatherox/Library/Application Support/Code - Insiders/logs/20191014T045453/exthost13/vscode.typescript-language-features/tsserver-log-w6HewS/tsserver.log --globalPlugins typescript-tslint-plugin --pluginProbeLocations /Users/ortatherox/.vscode-insiders/extensions/ms-vscode.vscode-typescript-tslint-plugin-1.2.2 --locale en --noGetErrOnBackgroundUpdate --validateDefaultNpmLocation
```

This log will contain file paths about the current project, and where your VS Code application is. If it's important to you, you may want to sanitize the log before sending it.

If the log is pretty small, you can embed it directly into an issue comment. If it's very long, we recommend you paste it into a gist at https://gist.github.com and link to that. If privacy is still a concern, we may be able to discuss a more private exchange of logs.

# Remember to Delete Your Old Logs

Over time, especially as editing sessions can span multiple days, your TS Server logs might start to accumulate. While we are investigating a lightweight way to avoid saving these files to disk all the time, it can be helpful to recover space by deleting your logs periodically.