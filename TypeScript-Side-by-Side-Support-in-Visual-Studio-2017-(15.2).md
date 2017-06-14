You can now use multiple versions of the TypeScript compiler in Visual Studio 2017 with Update 2 (15.2). During installation, TypeScript 2.2 will be automatically included with the Web, Node.js, Universal Windows, or Mobile JavaScript workloads. TypeScript 2.1 can also be selected from the 'Individual Components' installer page.

You can also install other versions of TypeScript from [TypeScript SDK for Visual Studio 2017 download page](https://www.microsoft.com/en-us/download/details.aspx?id=55258).

To set the version of TypeScript used by IntelliSense by using the setting shown in (Figure 1). 

![](https://www.visualstudio.com/en-us/news/releasenotes/media/tsversion-2.png)

(Figure 1) TypeScript Version Selection


To change the TypeScript version used for building a project, set the MSBuild property [`<TypeScriptToolsVersion>`](http://www.typescriptlang.org/docs/handbook/compiler-options-in-msbuild.html#toolsversion) in the project file. For more information on MSBuild properties, see the TypeScript Handbook.


