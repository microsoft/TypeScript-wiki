Using TypeScript with ASP.NET v5 requires that you setup your project in a specific way, for more information about ASP.NET v5 see the [ASP.NET v5 documentation](http://docs.asp.net/en/latest/conceptual-overview/index.html).

# Project setup

We start by creating a new empty ASP.NET v5 project in Visual Studio 2015, of you're not familiar with ASP.NET v5 follow [this tutorial](http://docs.asp.net/en/latest/tutorials/your-first-aspnet-application.html) for more information.

 ![Create new Empty ASP.NET Project](https://raw.githubusercontent.com/wiki/Microsoft/TypeScript/aspnet-screenshots/new-project.png)
 
 Next add a *scripts* folder to the root of the project, this is where we'll add the TypeScript files and the [tsconfig.json](tsconfig.json.md) file to set the compiler options.
 
![Project layout](https://raw.githubusercontent.com/wiki/Microsoft/TypeScript/aspnet-screenshots/project.png)

Finally we have to add the following option to the *"compilerOptions"* node in the *tsconfig.json* file to redirect the compiler output to the *wwwroot* folder:
````json
    "outDir": "../wwwroot/"
````
Now if you build your project, you notice the *app.js*, and the *app.js.map* files are created in the root of the *wwwroot* folder.

![Files after build](https://raw.githubusercontent.com/wiki/Microsoft/TypeScript/aspnet-screenshots/postbuild.png)


 
 
