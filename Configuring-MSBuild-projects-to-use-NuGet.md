For existing projects:
* Right-Click -> Manage NuGet Packages
* Search for `Microsoft.TypeScript.MSBuild`
* Hit `Install`
* Right-Click -> Unload Project
* Edit project file
* Replace:

 ```XML
 <Import 
Project="$(MSBuildExtensionsPath32)\Microsoft\VisualStudio\v$(VisualStudioVersion)\TypeScript\Microsoft.TypeScript.Default.props"
Condition="Exists('$(MSBuildExtensionsPath32)\Microsoft\VisualStudio\v$(VisualStudioVersion)\TypeScript\Microsoft.TypeScript.Default.props')" />
 ```
 with
 ```XML
  <Import 
Project="..\packages\Microsoft.TypeScript.MSBuild.1.8.0-beta\tools\Microsoft.TypeScript.Default.props"
Condition="Exists('..\packages\Microsoft.TypeScript.MSBuild.1.8.0-beta\tools\Microsoft.TypeScript.Default.props')" />
 ```
* Replace:
 ```XML
 <Import 
Project="$(MSBuildExtensionsPath32)\Microsoft\VisualStudio\v$(VisualStudioVersion)\TypeScript\Microsoft.TypeScript.targets"
Condition="Exists('$(MSBuildExtensionsPath32)\Microsoft\VisualStudio\v$(VisualStudioVersion)\TypeScript\Microsoft.TypeScript.targets')" />

 ```

 with
 ```XML
  <Import 
Project="..\packages\Microsoft.TypeScript.MSBuild.1.8.0-beta\tools\Microsoft.TypeScript.targets" 
Condition="Exists('..\packages\Microsoft.TypeScript.MSBuild.1.8.0-beta\tools\Microsoft.TypeScript.targets')" />
 ```
