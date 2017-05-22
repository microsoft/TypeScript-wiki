# Delivery Mechanism
## NuGet
Note: In release 2.3, TypeScript MSBuild has support for MSBuild cross-platform compilation.
### Package structure
TypeScript NuGet contains two main folders
* build folder

Two files are located in this folder:  *Microsoft.TypeScript.MSBuild.targets* and *Microsoft.TypeScript.MSBuild.props*.
Both are entry point for target file and props file respectively when using MSBuild with NuGet; *Microsoft.TypeScript.MSBuild.targets* will set variables needed for cross-platform compilation (e.g. Path to NodeJS etc.) and NuGet compilation (e.g. Path to corresponding Tasks.dll) before import *Microsoft.TypeScript.targets* in *tools* folder. *Microsoft.TypeScript.MSBuild.props* will set variables to indicate that the compilation is invoked through NuGet before import *Microsoft.TypeScript.Default.props* in *tools*.

* tools folder

## MSI Installer
### Folder structure
#### SDK
#### MSBuild

# Stub Target File
# Target File
# TypeScript.Tasks.dll & dependent DLL
# Limitation