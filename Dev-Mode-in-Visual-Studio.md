Dev Mode is a new feature for [TypeScript 1.4](https://github.com/Microsoft/TypeScript/releases/tag/v1.4) and higher that allows you to

1. Use a custom language service file of your choosing.
2. Debug the script side of the language service Visual Studio is using during a session.

# Enabling Dev Mode

## In Visual Studio 2013

1. Open up the Registry Editor (regedit.exe from the Run prompt).
2. Navigate to `HKEY_CURRENT_USER\Software\Microsoft\VisualStudio\12.0\TypeScriptLanguageService` (or create the key if it does not exist).
3. Create a new DWORD (32-bit) Value with the name `EnableDevMode`.
4. Right click the `EnableDevMode` value and **Modify** it.
5. Change the Value data to `1`.


## In Visual Studio 2015

1. Open up the Registry Editor (regedit.exe from the Run prompt).
2. Navigate to `HKEY_CURRENT_USER\Software\Microsoft\VisualStudio\14.0\TypeScriptLanguageService` (or create the key if it does not exist).
3. Create a new DWORD (32-bit) Value with the name `EnableDevMode`.
4. Right click the `EnableDevMode` value and **Modify** it.
5. Change the Value data to `1`.

# Using a custom language service file

1. [Enable dev mode](#Enabling-Dev-Mode)

# Debugging the language service

