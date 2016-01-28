For existing projects:
* Right-Click -> Manage NuGet Packages
* Search for `Microsoft.TypeScript.MSBuild`
 ![Search for NuGet package.](https://raw.githubusercontent.com/wiki/Microsoft/TypeScript/images/searchForNuGetPackage.png)

* Hit `Install`
* Right-Click -> Unload Project
* Edit project file
* Remove references to existing targets and prop files:

 ```XML
 <Import 
Project="$(MSBuildExtensionsPath32)\Microsoft\VisualStudio\v$(VisualStudioVersion)\TypeScript\Microsoft.TypeScript.Default.props"
Condition="Exists('$(MSBuildExtensionsPath32)\Microsoft\VisualStudio\v$(VisualStudioVersion)\TypeScript\Microsoft.TypeScript.Default.props')" />
 ```
 and
 
 ```XML
 <Import 
Project="$(MSBuildExtensionsPath32)\Microsoft\VisualStudio\v$(VisualStudioVersion)\TypeScript\Microsoft.TypeScript.targets"
Condition="Exists('$(MSBuildExtensionsPath32)\Microsoft\VisualStudio\v$(VisualStudioVersion)\TypeScript\Microsoft.TypeScript.targets')" />

 ```

* Reload Project