## Overview
Compiler options can be specified using MSBuild properties within an MSBuild project.

## Example

```XML
  <PropertyGroup Condition="'$(Configuration)' == 'Debug'">
    <TypeScriptRemoveComments>false</TypeScriptRemoveComments>
    <TypeScriptSourceMap>true</TypeScriptSourceMap>
  </PropertyGroup>
  <PropertyGroup Condition="'$(Configuration)' == 'Release'">
    <TypeScriptRemoveComments>true</TypeScriptRemoveComments>
    <TypeScriptSourceMap>false</TypeScriptSourceMap>
  </PropertyGroup>
  <Import Project="$(MSBuildExtensionsPath32)\Microsoft\VisualStudio\v$(VisualStudioVersion)\TypeScript\Microsoft.TypeScript.targets" Condition="Exists('$(MSBuildExtensionsPath32)\Microsoft\VisualStudio\v$(VisualStudioVersion)\TypeScript\Microsoft.TypeScript.targets')" />
```

## Mappings

Compiler Option   | MSBuild Property Name | Allowed Values
------------------|-----------------------|-----------------
--declaration     | TypeScriptGeneratesDeclarations | boolean
--module          | TypeScriptModuleKind  | AMD or CommonJs
--target          | TypeScriptTarget      | ES3, ES5 or ES6
--charset         | TypeScriptCharset     |                           
--emitBOM         | TypeScriptEmitBOM     | boolean     
--locale          | Automatically set to PreferredUILang value |
--mapRoot         | TypeScriptMapRoot     | File path 
--noEmitOnError   | TypeScriptNoEmitOnError | boolean
--noImplicitAny   | TypeScriptNoImplicitAny | boolean
--noLib           | TypeScriptNoLib       | boolean
--noResolve       | TypeScriptNoResolve   | boolean
--out             | TypeScriptOutFile     | File path
--outDir          | TypeScriptOutDir      | File path
--preserveConstEnums | TypeScriptPreserveConstEnums | boolean
--removeComments  | TypeScriptRemoveComments | boolean
--sourceMap       | TypeScriptSourceMap | File path                         
--sourceRoot      | TypeScriptSourceRoot | File path
--suppressImplicitAnyIndexErrors | TypeScriptSuppressImplicitAnyIndexErrors | boolean
--project         | *Not supported in VS* | 
--watch           | *Not supported in VS* |
--diagnostics     | *Not supported in VS* |
--listFiles       | *Not supported in VS* |
--noEmit          | *Not supported in VS* |
