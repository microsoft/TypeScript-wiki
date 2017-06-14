In Visual Studio 2017, TypeScript updates are applied slightly differently than in Visual Studio 2015, however you can now have multiple TypeScript versions installed and choose specific versions for each of your projects.
In this document, we will walk through how to manage your TypeScript version in Visual Studio 2017.

## Pre-reqs

You will need Visual Studio 2017 version 15.2 or later in order to change your TypeScript version. 
Depending on which version you have, there are slightly different instructions for managing your TypeScript versions.
That said, using the latest available version of Visual Studio will provide the best experience.  

## Configuring TypeScript versions in Visual Studio 2017 version 15.2

During installation of Visual Studio 2017 version 15.2, TypeScript 2.2 will be automatically included with the Web, Node.js, Universal Windows, or Mobile JavaScript workloads. TypeScript 2.1 can also be selected from the 'Individual Components' installer page. 

To change TypeScript versions, change the following setting:

1. From the top menu bar, open **Tools > Options > Text Editor > JavaScript/TypeScript > IntelliSense**
2. Change **Use TypeScript version** to your desired version

![](https://www.visualstudio.com/en-us/news/releasenotes/media/tsversion-2.png)
(Figure 1) TypeScript Version Selection

> Don't see the version you're looking for?  
Make sure you have downloaded the correct SDK version from the [download center](https://www.microsoft.com/en-us/download/details.aspx?id=55258).

To change the TypeScript version used for **building** a project, set the MSBuild property [`<TypeScriptToolsVersions>`](http://www.typescriptlang.org/docs/handbook/compiler-options-in-msbuild.html#toolsversion) in the project file. For more information on MSBuild properties, see the TypeScript Handbook.

In Visual Studio 2017 version 15.2 you are limited to setting a global TypeScript version.
This means that if you have two projects that use different TypeScript compiler versions, you will have to manually toggle the setting each time you switch projects.
This is not ideal, so if possible, upgrade to Visual Studio 2017 15.3 and follow the instructions below.

## Configuring TypeScript versions in Visual Studio 2017 version 15.3

In Visual Studio 2017 version 15.3 and later, the TypeScript version used is bound to individual projects.

1. Right click on the project node in Solution Explorer 
2. Click **Properties**
3. Go to the **TypeScript Build** tab
4. Change **TypeScript version** to the desired version or "use latest available" to always default to the newest version installed

![15.3 properties page](./15.3-properties.png "15.3 properties page")

When setting a TypeScript version on a project, the project becomes fixed on the that version.
If a new TypeScript version becomes available through a Visual Studio update or a manual SDK download, the project will **still use the version it is fixed to**.
To stay on the latest version, we encourage you to set your projects to "use latest available" as described in step 4 above.

> Note! If multiple projects are loaded with different TypeScript versions set in the properties page, the **latest** TypeScript version of all versions specified will take precedence.