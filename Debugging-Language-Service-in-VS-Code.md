VS Code is designed around an extension model. This means that the client-side (ie: text-editor) code for communicating with the TypeScript server lives in "extensions/typescript" in the VS Code repo.<sup>1</sup>
The server side code lives in `src/services` of the TypeScript repo.

We will use a stable version of vscode to debug a development version of vs code running against a development version of tsserver.

1) Download/install a stable version of vs code.

2) Follow the instructions to [setup a development version of vs code](https://github.com/Microsoft/vscode/wiki/How-to-Contribute).<sup>1</sup>

3) Clone the typescript repo locally, and follow the instructions on building typescript.

4) [Update your user settings](https://code.visualstudio.com/docs/languages/typescript#_using-newer-typescript-versions) to use your development version of typescript, located in the `.../TypeScript/built/local` directory.
The corresponding setting/path is
```
{
   "typescript.tsdk": "/path/to/repo/TypeScript/built/local"
}
```


From here, there are different steps for debugging the client- and server-side, respectively.

## Debugging client-side

**Note:** [the gulp-build doesn't currently produce working source-maps](https://github.com/Microsoft/TypeScript/issues/11105), and [building with jake may require some extra effort to fix the source-maps](https://github.com/Microsoft/TypeScript/issues/11111).

1) Set the ts-server to be open in debug mode on the right port using either of the following two methods (in the rest of this guide, we assume you chose 5859):

a. In a shell, [export](http://stackoverflow.com/questions/1158091/defining-a-variable-with-or-without-export) the `TSS_DEBUG` environment variable to an open port (you will run the development vs code instance from within that shell).

b. Edit `extensions/typescript/src/typescriptServiceClient.ts`, setting the port to an open one.

2) Update launch.json with an option to attach to the node instance, with sourcemaps from your `built/local` folder. You can use this option as a template:
```
		{
			"name": "Attach to TS Server",
			"type": "node",
			"request": "attach",
			"port": 5859,
			"sourceMaps": true,
			"outDir": "/path/to/repo/TypeScript/built/local"
		},
```

3) Launch an instance of development vs code, and open a ts file.

4) Launch an instance of stable vs code.

5) Attach the stable vs code instance to the development instance.

## Debugging server-side

3) Launch an instance of development vs code.

4) Launch an instance of stable vs code.

5) Attach the stable vs code instance to the development instance.


---
<sup>1</sup> In particular, the built-in extension spawns the node instance that loads tsserver via the call to electron.fork() in `extensions/typescript/src/typescriptServiceClient.ts`.

<sup>2</sup> If you are on Linux, be sure to increase the number of file watchers per the fix for ENOSPC [errors](https://github.com/Microsoft/vscode/wiki/How-to-Contribute#incremental-build). for opening medium-large projects like Typescript, the default limit of 8192 is almost certainly too small.
