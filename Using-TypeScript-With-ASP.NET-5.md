Using TypeScript with ASP.NET v5 requires that you setup your project in a specific way.
For more information about ASP.NET v5 see the [ASP.NET v5 documentation](http://docs.asp.net/en/latest/conceptual-overview/index.html).
The current tsconfig.json support in Visual Studio projects is a work in progress, which is tracked in issue [#3983](https://github.com/Microsoft/TypeScript/issues/3983).

# Project setup

We start by creating a new empty ASP.NET v5 project in Visual Studio 2015, of you're not familiar with ASP.NET v5 follow [this tutorial](http://docs.asp.net/en/latest/tutorials/your-first-aspnet-application.html) for more information.

 ![Create new Empty ASP.NET Project](https://raw.githubusercontent.com/wiki/Microsoft/TypeScript/aspnet-screenshots/new-project.png)
 
Next add a `scripts` folder to the root of our project.
This is where we'll add the TypeScript files and the [`tsconfig.json`](tsconfig.json.md) file to set our compiler options. 
Please note that the names and locations of the folders are pertinent to get the solution working correctly.
To add a `tsconfig.json` file, simply right click on the `scripts` folder, navigate to `Add`, then `New Item`.
Under `Client-side`, you can find it, as can be seen below.

*Note:* in since Visual Studio 2015 Update 1 RC, we also support adding the [`tsconfig.json`](tsconfig.json.md) file
to the root of your project. Take care to adjust the `outDir` property appropiatly.

![Adding a 'tsconfig.json' file in Visual Studio](https://raw.githubusercontent.com/wiki/Microsoft/TypeScript/aspnet-screenshots/add-tsconfig.png)
 
![A project in Visual Studio's Solution Explorer](https://raw.githubusercontent.com/wiki/Microsoft/TypeScript/aspnet-screenshots/project.png)

Finally we have to add the following option to the `"compilerOptions"` node in the `tsconfig.json` file to redirect the compiler output to the `wwwroot` folder:

```json
    "outDir": "../wwwroot/"
```

This is potentially what a tsconfig.json might look like once configured.

```json
{
    "compilerOptions": {
        "noImplicitAny": false,
        "noEmitOnError": true,
        "removeComments": false,
        "sourceMap": true,
        "target": "es5",
        "outDir": "../wwwroot"
    }
}
```

Now if we build our project, you'll notice the `app.js` and `app.js.map` files were created in the root of our `wwwroot` folder.

![Files after build](https://raw.githubusercontent.com/wiki/Microsoft/TypeScript/aspnet-screenshots/postbuild.png)

## Project vs Virtual Project

When adding a `tsconfig.json` file, it is vital to understand that this creates a virtual typescript project within the folder where the `tsconfig.json` is located.
TypeScript files that are considered part of this virtual project will not be compiled when saving changes.
TypeScript files that are *outside* of the folder containing `tsconfig.json` are *not* considered part of the virtual project.
In the image below, the virtual project can be visualized, and is the that which is contained within the red rectangle.

![A virtual project in Visual Studio's Solution Explorer](https://raw.githubusercontent.com/wiki/Microsoft/TypeScript/aspnet-screenshots/virtual-project.png)