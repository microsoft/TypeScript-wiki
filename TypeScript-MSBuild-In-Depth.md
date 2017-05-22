# Delivery Mechanism

## NuGet

In release 2.3 and above, TypeScript MSBuild can be used with MSBuild on non-Window platform.

### Package structure

TypeScript NuGet contains two main folders

* build folder

    Two files are locate in the folder.
    Both are entry point for main TypeScript target file and props file respectively.

    1. Microsoft.TypeScript.MSBuild.targets

        The file set variables specific to a run-time platform, such as a path to `TypeScript.Tasks.dll`, before import `Microsoft.TypeScript.targets` from `tools` folder.

    2. Microsoft.TypeScript.MSBuild.props

        The file imports `Microsoft.TypeScript.Default.props` from `tools` folder and set properties indicating that the build is initiated through NuGet.

* tools folder
    
    Earlier version only contains tsc folder. `Microsoft.TypeScript.targets` and `TypeScript.Tasks.dll` are located at the root level.

    Below structure is for version 2.3 and above.
    1. net45

        contains `Microsoft.TypeScript.targets`, `TypeScript.Tasks.dll` and facade dependency dlls.
        When building any projecton Window platform, MSBuild uses the DLLs from this folder.

    2. netstandard

        contains `Microsoft.TypeScript.targets` and `TypeScript.Tasks.dll`.
        The contents in this folder are used when building projects on non-Window machine.

    3. tsc

        contains `tsc.exe` and all dependency files to execute the exe.
        On Window, `TypeScript.Tasks.dll` uses the `tsc.exe` to build projects.
        On non-Window, `TypeScript.Tasks.dll` uses NodeJS to run `tsc.js`.
        So, it is required that NodeJS is installed.

## MSI Installer
    
TypeScript is also shipped as a stand-alone exe that can be installed on Window machine.
Currently the installer is only available for Visual Studio 2015 with Update 3.

The installer install files into following folders:

1. Microsoft SDKs Folder

    The installer install a sub-folder called `TypeScript` into `%ProgramFile%/Microsoft SDKs`.

    * Before version 2.3

        A version-number folder is installed inside the TypeScript sub-folder in which the version-number folder contains all neccessary files to execute `tsc.exe`.

    * Version 2.3 and above

        In additional to similar contents with prior version, the newer installer installe another sub-folder inside the version-number folder called *build*.
        This *build* folder contains `Microsoft.TypeScript.targets`, `TypeScript.Tasks.dll` and its dependencies.
        This allows us to be able to support side-by-side while being able to make any neccessary changes to the task and target file.
        We also add another sub-folder called *versions* which is used by the stub target file (see: Stub Target File section) to figure out the version to use.

2. MSBuild folder

    The installer install a folder call *TypeScript* to `%ProgramFile%\MSBuild\Microsoft\VisualStudio\v14.0`

    * Before version 2.3

        The folder mainly contains the TypeScript task and target file.
        These files include main logic to build and compile TypeScript project.

    * Version 2.3 and above

        The folder only contains an entry point target file which although has same name as earlier version target file, it has very different contents.
        This new target file is what we call stub-target file (See: Stub Target File section)
        It is worth noting that the installer will *NOT* install the task dll into this location anymore.

# Stub Target File

This file is introduced in version 2.3 and later. It is to replaced the original target file that is moved into *build* inside version-number folder in `%ProgramFile%/Microsoft SDKs/TypeScript` folder.
The file load a file in *versions* folder from `%ProgramFile%/Microsoft SDKs/TypeScript`.
This mechanism will sort file in lexicographically, allowing us to pick latest version.
If the version can't be figure out, the stub target will load the one next to it.
