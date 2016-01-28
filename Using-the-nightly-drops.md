## For building using nodejs use `typescript@next`:

```cmd
npm install -g typescript@next
```

## For building using MSBuild, use NuGet:

> Note: You need to configure your project to use the NuGet packages to build instead of the tools installed on the machine.

The nightlies are available on https://www.myget.org/gallery/typescript-preview

There are two packages:
* Microsoft.TypeScript.Compiler: Tools only (tsc.exe, lib.d.ts, etc..) 
* Microsoft.TypeScript.MSBuild: Tools as above + MSBuild tasks and tragets (Microsoft.TypeScript.targets, Microsoft.TypeScript.Default.props, etc..)


## VS Code 

* First, install the npm package `npm install typescript@next`, to your local node_modules folder, then
* Update, `.vscode/settings.json` with the following:
>  "typescript.tsdk": "<path to your folder>/node_modules/typescript/lib"


## Sublime Text

> Note: This is currently a manual process; issue https://github.com/Microsoft/TypeScript-Sublime-Plugin/issues/370 tracks automating the process.

* First, install the npm package `npm install typescript@next`, to your local node_modules folder, then
* Go to your Packages folder
 * Mac/Linux: `~/"Library/Application Support/Sublime Text 3/Packages"`
 * Windows:  `"%APPDATA%\Sublime Text 3\Packages"`
* Copy:
 * `<path to your folder>\node_modules\typescript\lib\tsserver.js` to `<Packages>\TypeScript\tsserver\`
 * `<path to your folder>\node_modules\typescript\lib\lib.d.ts` to `<Packages>\TypeScript\tsserver\`

More information is available at: https://github.com/Microsoft/TypeScript-Sublime-Plugin#installation

## Visual Studio 2013 and 2015

> Note: Most changes do not require you to install a new version of the VS TypeScript plugin in. The nightly build currently does not include the full plugin setup, we are working on getting an installer published nightly along with the npm package

* First, install the npm package `npm install typescript@next`, to your local node_modules folder, then
* Download the script in https://github.com/Microsoft/TypeScript/blob/master/scripts/VSDevMode.ps1
 * You can do the same with the script by following the steps in [Using custom language service file wiki page](https://github.com/Microsoft/TypeScript/wiki/Dev-Mode-in-Visual-Studio#using-a-custom-language-service-file) 
* From a poweshell command window, run:
 * VS 2015:
 ```cmd 
 VSDevMode.ps1 14 -tsScript <path to your folder>\node_modules\typescript\lib
 ```
 * VS 2013:
 ```cmd 
 VSDevMode.ps1 12 -tsScript <path to your folder>\node_modules\typescript\lib
 ```
