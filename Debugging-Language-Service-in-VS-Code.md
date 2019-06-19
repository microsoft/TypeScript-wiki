VS Code is designed around an extension model. TypeScript provides a server called TSServer that provides information for providing quick-info, completions, etc., and VS Code acts as a client which queries the server when this information is needed. For example, VS Code queries TSServer for quick-info when the user's mouse hovers over a variable. TSServer will respond with information such as the appropriate type, and the styling to apply to the text that describes the type.

Organizationally, the client-side code for communicating with the TypeScript server lives in [`extensions/typescript-language-features`](https://github.com/Microsoft/vscode/tree/master/extensions/typescript-language-features) in [the VS Code repository](https://github.com/Microsoft/vscode).<sup>1</sup>

Meanwhile, the server-side code lives in `src/services` and `src/server` of [the TypeScript repository](https://github.com/Microsoft/TypeScript).

We will use a stable version of VS Code to debug a development version of VS Code running against a development version of TSServer.

1. Download/install a stable version of vs code.
2. Follow the instructions to [setup a development version of VS Code](https://github.com/Microsoft/vscode/wiki/How-to-Contribute).<sup>2</sup>
3. Clone the TypeScript repo locally, and follow the instructions on [building TypeScript](https://github.com/Microsoft/TypeScript#building).
4. [Update the User Settings](https://code.visualstudio.com/docs/languages/typescript#_using-newer-typescript-versions) in the development version of VS Code, to point to the `built/local` directory of your local TypeScript repository.

   This will look something like the following:

   ```json
   {
      "typescript.tsdk": "/path/to/repo/TypeScript/built/local"
   }
   ```

   You may instead update this in the Workspace Settings for a project as well, but you will have to remember that the development version of TSServer will only be in effect within that project.

From here, there are different steps for debugging the client- and server-side, respectively.

## Debugging tsserver (server-side)

> **Note:** [the gulp-build doesn't currently produce working source-maps](https://github.com/Microsoft/TypeScript/issues/11105), and [building with jake may require some extra effort to fix the source-maps](https://github.com/Microsoft/TypeScript/issues/11111).

1. Choose an available port to debug TSServer using either of the following two methods (in the rest of this guide, we assume you chose 5859):
    * In a shell, export the `TSS_DEBUG` environment variable to an open port. We will run the development VS Code instance from within that shell.

    For most Unix-like shells (e.g. bash), this will be something like

    ```sh
    export TSS_DEBUG=5859
    ```

    For PowerShell, this is something like

    ```posh
    $env:TSS_DEBUG = 5859
    ```

    * Alternatively, manually edit `extensions/typescript/src/typescriptServiceClient.ts` in your development-side VS Code, setting the port to an open one.

2. Update `launch.json` with an option to attach to the node instance, with sourcemaps from your `built/local` folder.

   For VS Code v1.13.1+ and Node v8.0+, your `launch.json` might look like the following:

   ```json5
   {
       "version": "0.2.0",
       "configurations": [
           // Other configs
           {
               "name": "Attach to TS Server",
               "type": "node",
               "request": "launch",
               "protocol": "inspector",
               "port": 5859,
               "sourceMaps": true,
               "outFiles": ["/path/to/repo/TypeScript/built/local"],
               "runtimeArgs": [
                    "--inspect=5859"
               ]
           }
       ]
   }
   ```
   
   For the same versions of Code, but older versions of Node (e.g. 6.x), you'll need to set `"protocol"` to be `"legacy"`.

3. Launch an instance of your development VS Code, and open a TypeScript file.
4. Launch your stable-release version of VS Code.
5. Attach the stable VS Code instance to the development instance.

## Debugging the Extension Host (client-side)

3) Launch an instance of development vs code.

4) Launch an instance of stable vs code.

5) Attach the stable vs code instance to the development instance.


---
<sup>1</sup> In particular, the built-in extension spawns the node instance that loads tsserver via the call to electron.fork() in `extensions/typescript/src/typescriptServiceClient.ts`.

<sup>2</sup> If you are on Linux, be sure to increase the number of file watchers per the fix for ENOSPC [errors](https://github.com/Microsoft/vscode/wiki/How-to-Contribute#incremental-build). for opening medium-large projects like Typescript, the default limit of 8192 is almost certainly too small.
