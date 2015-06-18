# Visual Studio 2013/2015

[Visual Studio](https://www.visualstudio.com/) comes with TypeScript when installing Microsoft Web Tools.

# Visual Studio Code

[Visual Studio Code](https://code.visualstudio.com/), a lightweight cross-platform editor, comes with TypeScript support built in.

# Sublime Text 3/2

[TypeScript Plugin for Sublime](https://github.com/Microsoft/TypeScript-Sublime-Plugin) can be acquired through [Package Control](https://packagecontrol.io/).

# Atom

# Eclipse
[TypeScript Plugin for Eclipse](https://github.com/palantir/eclipse-typescript), a TypeScript language service support for Eclipse IDE.

# Vim

### Syntax highlight
[leafgarland/typescript-vim](https://github.com/leafgarland/typescript-vim) provides syntax files for highlighting .ts and .d.ts files. 

### Language Service Tools
There are two main TypeScript plugins:
* [clausreinke/typescript-tools.vim](https://github.com/clausreinke/typescript-tools.vim)
* [Quramy/tsuquyomi](https://github.com/Quramy/tsuquyomi)

If you would like to have as-you-type completion, you can install [YouCompleteMe](https://github.com/Valloric/YouCompleteMe) and add the following codes into your .vimrc to specify what token will trigger the completion. YouCompleteMe will call into your respective TypeScript Plugin for semantic inquiry.

```vimscript
if !exists("g:ycm_semantic_triggers")
 let g:ycm_semantic_triggers = {}
endif
let g:ycm_semantic_triggers['typescript'] = ['.']
```
