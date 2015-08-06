Using TypeScript with ASP.NET v5 requires that you setup your project in a specific way.
For more information about ASP.NET v5 see the [ASP.NET v5 documentation](http://docs.asp.net/en/latest/conceptual-overview/index.html).
The current tsconfig.json support in Visual Studio projects is a work in progress, which is tracked in issue [#3983](https://github.com/Microsoft/TypeScript/issues/3983).

# Project setup

We start by creating a new empty ASP.NET v5 project in Visual Studio 2015, of you're not familiar with ASP.NET v5 follow [this tutorial](http://docs.asp.net/en/latest/tutorials/your-first-aspnet-application.html) for more information.

 ![Create new Empty ASP.NET Project](https://raw.githubusercontent.com/wiki/Microsoft/TypeScript/aspnet-screenshots/new-project.png)
 
Next add a `scripts` folder to the root of our project.
This is where we'll add the TypeScript files and the [tsconfig.json](tsconfig.json.md) file to set our compiler options.
 
![Project layout](https://raw.githubusercontent.com/wiki/Microsoft/TypeScript/aspnet-screenshots/project.png)

Finally we have to add the following option to the `"compilerOptions"` node in the `tsconfig.json` file to redirect the compiler output to the `wwwroot` folder:

```json
    "outDir": "../wwwroot/"
```

Now if we build our project, you'll notice the `app.js` and `app.js.map` files were created in the root of our `wwwroot` folder.

![Files after build](https://raw.githubusercontent.com/wiki/Microsoft/TypeScript/aspnet-screenshots/postbuild.png)

##Compile on Save
In order to enable *Compile on Save* for ASP.NET v5 projects, you have to enable *Compile on Save* for TypeScript files which are not part of a project. The setting for the selected module type in that dialog will be ignored if a `tsconfig.json` file is part of the project.

![Compile on Save](https://raw.githubusercontent.com/wiki/Microsoft/TypeScript/aspnet-screenshots/compile-on-save.png)