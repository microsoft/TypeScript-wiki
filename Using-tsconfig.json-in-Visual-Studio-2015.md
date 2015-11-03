## Overview

[`tsconfig.json`](tsconfig.json.md) has limited support in Visual Studio 2015.
Current support is limited to 'loose files', ASP.NET v5, and Apache Cordova Apps.

*Note:* At this time we don't support any other project types in Visual Studio using a `tsconfig.json` file.
This includes the 'HTML Application with TypeScript' and ASP.NET v4.

## Loose Files

Any time you open a TypeScript project in Visual Studio, we will do a search for a `tsconfig.json` file by traversing up each
directory to the root directory, or until we find a `tsconfig.json` file.
If you have the option to 'Display Virtual Projects when no Solution is loaded' enabled, you'll see the files associated with that `tsconfig.json` in the *Solution Explorer* view.
This view is a representation of the view the TypeScript compiler has of your files.

## ASP.NET v5 and Apache Cordova Apps

This is a detailed description for [using TypeScript with ASP.NET 5](Using-TypeScript-With-ASP.NET-5.md).
The configuration for Apache Cordova projects is identical. However the `tsconfig.json` template for that project type
has some other defaults. 

## Limitations

There are a number of limitations with the Loose Files scenario:
 
* Since TypeScript doesn't have a real project system, there is no right-click menu in the Solution Explorer.
In order to add new files, you have to use the File >> New >> File... (Ctrl + N) menu item.
Further, if you want to configure compilation options, you will have to open the 'tsconfig.json' file and edit its settings.

* Because 'tsconfig.json' is the way to configure the behavior of the TypeScript compiler, and not a complete project, 
it only includes TypeScript files in its view. If you want to work with TypeScript and other files the best way to that is to create an ASP.NET v5 project.

* We only support a single `tsconfig.json` file per project.

## Compile on Save

In order to enable *Compile on Save* for ASP.NET v5 projects, you have to enable *Compile on Save* for TypeScript files which are not part of a virtual TypeScript project.
The settings for the selected module type and ECMAScript version will be ignored if a `tsconfig.json` file is part of the project.

![Compile on Save](https://raw.githubusercontent.com/wiki/Microsoft/TypeScript/aspnet-screenshots/compile-on-save.png)
