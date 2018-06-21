# Delivery Mechanism

## NuGet

In releases 2.3 and above, TypeScript MSBuild can be used with MSBuild on non-Windows platforms.

### Package structure

TypeScript NuGet contains two main folders

* build folder

    Two files are located in this folder.
    Both are entry points - for the main TypeScript target file and props file respectively.

    1. Microsoft.TypeScript.MSBuild.targets

        The file set variables specific to a run-time platform, such as a path to `TypeScript.Tasks.dll`, before importing `Microsoft.TypeScript.targets` from `tools` folder.

    2. Microsoft.TypeScript.MSBuild.props

        The file imports `Microsoft.TypeScript.Default.props` from the `tools` folder and sets properties indicating that the build has been initiated through NuGet.

* tools folder
    
    Earlier versions only contain a tsc folder. `Microsoft.TypeScript.targets` and `TypeScript.Tasks.dll` are located at the root level.

    The below structure is for versions 2.3 and above.
    1. net45

        contains `Microsoft.TypeScript.targets`, `TypeScript.Tasks.dll` and facade dependency dlls.
        When building any project on a Windows platform, MSBuild uses the DLLs from this folder.

    2. netstandard

        contains `Microsoft.TypeScript.targets` and `TypeScript.Tasks.dll`.
        The contents in this folder are used when building projects on a non-Window machine.

    3. tsc

        contains `tsc.exe` and all dependency files required to execute the exe.
        On Windows, `TypeScript.Tasks.dll` uses the `tsc.exe` to build projects.
        On non-Windows, `TypeScript.Tasks.dll` uses NodeJS to run `tsc.js` - implying that it is required for NodeJS to be installed on these platforms.

## MSI Installer
    
TypeScript is also shipped as a stand-alone exe that can be installed on a Windows machine.
Currently the installer is only available for Visual Studio 2015 with Update 3.

The installer install files into following folders:

1. Microsoft SDKs Folder

    The installer installs a sub-folder called `TypeScript` into `"%ProgramFiles%\Microsoft SDKs"`.

    * Before version 2.3

        A version-number folder is installed inside the TypeScript sub-folder in which the version-number folder contains all necessary files to execute `tsc.exe`.

    * Version 2.3 and above

        In additional to similar contents with prior version, the newer installer installs another sub-folder inside the version-number folder called *build*.
        This *build* folder contains `Microsoft.TypeScript.targets`, `TypeScript.Tasks.dll` and its dependencies.
        This allows us to be able to support side-by-side while being able to make any necessary changes to the task and target file.
        We also add another sub-folder called *versions* which is used by the stub target file (see: Stub Target File section) to figure out the version to use.

2. MSBuild folder

    The installer install a folder call *TypeScript* to `%ProgramFile%\MSBuild\Microsoft\VisualStudio\v14.0`

    * Before version 2.3

        The folder mainly contains the TypeScript task and target file.
        These files include main logic to build and compile a TypeScript project.

    * Version 2.3 and above

        The folder only contains an entry point target file which although has same name as earlier version target file, it has very different contents.
        This new target file is what we call a stub-target file (See: Stub Target File section)
        It is worth noting that the installer will *NOT* install the task dll into this location anymore.

# Stub Target File

This file is introduced in version 2.3 and later. It replaces the original target file that has moved into *build* inside the version-numbered folder in the `%ProgramFile%/Microsoft SDKs/TypeScript` folder.
The file will load a file in the *versions* folder from `%ProgramFile%/Microsoft SDKs/TypeScript`.
The latest version is determined by the lexicographic ordering of the files within this folder.
If the version cannot be determined, the stub target will load the version adjacent to it.
