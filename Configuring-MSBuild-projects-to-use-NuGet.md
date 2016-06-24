> **Note**: The install script will remove the default import to the `Microsoft.TypeScript.targets` file;
if you have manually edited the import before, you will need to remove it yourself **before** proceeding.
See [Removing default imports](#removing-default-imports) for more details.

> **Note**: The Nuget package depends on the x86 version of [Visual C++ Redistributable for Visual Studio 2015]
(https://www.microsoft.com/en-us/download/details.aspx?id=48145).
This is generally already installed on your computer, but you can verify that within **Programs and Features**.

## For major releases (https://www.nuget.org)

* Right-Click -> Manage NuGet Packages
* Search for `Microsoft.TypeScript.MSBuild`
 ![Search for NuGet package.](https://raw.githubusercontent.com/wiki/Microsoft/TypeScript/images/searchForNuGetPackage.png)

* Hit `Install`
* When install is complete, rebuild!


## For Nightly drops (https://www.myget.org)

1. Add a new Package Source 
 * Go to `Tools` -> `Options` -> `NuGet Package Manager` -> `Package Sources`
 * Create a new Source:
  * Name: `TypeScript Nightly`
  * Source: `https://www.myget.org/F/typescript-preview/`
 ![Add new Package Source.](https://raw.githubusercontent.com/wiki/Microsoft/TypeScript/images/addNewPackageSource.PNG)

2. Use the new Package Source
 * On Project node Right-Click -> `Manage NuGet Packages`
 * Search for `Microsoft.TypeScript.MSBuild`
 ![Search for NuGet package.](https://raw.githubusercontent.com/wiki/Microsoft/TypeScript/images/searchForMyGetPackage.PNG)
 * Hit `Install`
 * When install is complete, rebuild!


## Removing default imports

* Right-Click -> `Unload Project`
* Right-Click -> `Edit <project file name>`
* Remove references to

 * `Microsoft.TypeScript.Default.props`

   The import should look something like:

     ```XML
<Import
         Project="$(MSBuildExtensionsPath32)\Microsoft\VisualStudio\v$(VisualStudioVersion)\TypeScript\Microsoft.TypeScript.Default.props"
         Condition="Exists('$(MSBuildExtensionsPath32)\Microsoft\VisualStudio\v$(VisualStudioVersion)\TypeScript\Microsoft.TypeScript.Default.props')" />
     ```

 * `Microsoft.TypeScript.targets`

   The import should look something like:

     ```XML
<Import
        Project="$(MSBuildExtensionsPath32)\Microsoft\VisualStudio\v$(VisualStudioVersion)\TypeScript\Microsoft.TypeScript.targets"
        Condition="Exists('$(MSBuildExtensionsPath32)\Microsoft\VisualStudio\v$(VisualStudioVersion)\TypeScript\Microsoft.TypeScript.targets')" />
     ```
