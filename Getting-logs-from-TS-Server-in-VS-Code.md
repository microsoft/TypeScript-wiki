This page describes how to get a TS Server log via VS Code - you might need this for debugging why a crash has occurred, or to make sure that the communication between TypeScript the language and your editor is consistent.

1. Open the project you want to investigate in VS Code
1. Run the command `Open TS server log file` (View > Command Palette) , this should offer to turn on logging if you don't have it enabled
1. Restart your project, and try to only do the reproduction case
1. Run `Open TS server log file` again and it will re-direct you to a file with the logs

The log should start like:

```
Info 0    [13:5:26.815] Starting TS Server
Info 1    [13:5:26.816] Version: 3.7.0-dev.20190922
Info 2    [13:5:26.816] Arguments: /Applications/Visual Studio Code - Insiders.app/Contents/Frameworks/Code - Insiders Helper.app/Contents/MacOS/Code - Insiders Helper /Users/ortatherox/dev/typescript/TypeScript/node_modules/typescript/lib/tsserver.js --useInferredProjectPerProjectRoot --enableTelemetry --cancellationPipeName /var/folders/3d/j0zt8n5d77n_4mthj6nsb6h00000gn/T/vscode-typescript501/58d39090e29f4276f14f/tscancellation-e9842eb530b8e993176a.tmp* --logVerbosity verbose --logFile /Users/ortatherox/Library/Application Support/Code - Insiders/logs/20191014T045453/exthost13/vscode.typescript-language-features/tsserver-log-w6HewS/tsserver.log --globalPlugins typescript-tslint-plugin --pluginProbeLocations /Users/ortatherox/.vscode-insiders/extensions/ms-vscode.vscode-typescript-tslint-plugin-1.2.2 --locale en --noGetErrOnBackgroundUpdate --validateDefaultNpmLocation
```

This log will contain file paths about the current project, and where your VS Code application is. If may want to sanitize the log before sending it. 

If the log is pretty small, you can embed it directly into an issue comment. If it's very long, we recommend you paste it into a gist at https://gist.github.com and link to that.