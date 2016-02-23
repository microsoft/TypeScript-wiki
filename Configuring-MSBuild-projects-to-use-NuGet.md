## For major releases (www.nuget.org)

* Right-Click -> Manage NuGet Packages
* Search for `Microsoft.TypeScript.MSBuild`
 ![Search for NuGet package.](https://raw.githubusercontent.com/wiki/Microsoft/TypeScript/images/searchForNuGetPackage.png)

* Hit `Install`
* When install is complete, rebuild!


## For Nightly dorps (www.myget.org)

1. Add a new Package Source 
 * Go to `Tools` -> `Options` -> `NuGet Package Manager` -> `Package Sources`
 * Create a new Source:
  * Name: `TypeScript Nightly`
  * Source: `https://www.myget.org/F/typescript-preview/`
 ![Add new Package Source.](https://raw.githubusercontent.com/wiki/Microsoft/TypeScript/images/addNewPackageSource.png)

2. Use the new Package Source
* On Project node Right-Click -> `Manage NuGet Packages`
* Search for `Microsoft.TypeScript.MSBuild`
 ![Search for NuGet package.](https://raw.githubusercontent.com/wiki/Microsoft/TypeScript/images/searchForMyGetPackage.png)
* Hit `Install`
* When install is complete, rebuild!