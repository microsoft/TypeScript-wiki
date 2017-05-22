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
        On non-Window, `TypeScript.Tasks.dll` uses `tsc.js`, running through command

        ```
            node tsc.js <project path>
        ```

        Therefore, on non-Window platform, NodeJS is required to be installed.