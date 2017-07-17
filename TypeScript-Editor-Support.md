# Quick List

 * [alm.tools](#alm)
 * [Atom](#atom)
 * [CATS](#cats) 
 * [Eclipse](#eclipse)
 * [Emacs](#emacs)
 * [NeoVim](#neovim)
 * [NetBeans](#netbeans)
 * [Notepad++](#notepad)
 * [Sublime Text](#sublime-text)
 * [Vim](#vim)
 * [Visual Studio](#visual-studio-20132015)
 * [Visual Studio Code](#visual-studio-code)
 * [WebStorm](#webstorm)

# alm

[alm.tools](http://alm.tools/) a complete TypeScript development environment available as a simple npm package.

```shell
npm i alm -g
```

# Atom

[Atom-TypeScript](https://atom.io/packages/atom-typescript), a TypeScript language service for Atom developed by TypeStrong

# CATS

[Code Assistant for TypeScript (CATS)](https://github.com/jbaron/cats) -  is an open source TypeScript development environment that runs on OS X, Windows, and Linux.
Since it is also written in TypeScript, it can be easily extended if required.

# Eclipse

* [Eclipse TypeScript Plug-in](https://github.com/palantir/eclipse-typescript), an Eclipse plugin developed by Palantir.
* [typescript.java](https://github.com/angelozerr/typescript.java) an Eclipse plugin which consumes tsserver with JSDT Editor.

# Emacs

[tide](https://github.com/ananthakumaran/tide) - TypeScript Interactive Development Environment for Emacs

# NeoVim

[nvim-typescript](https://github.com/mhartington/nvim-typescript)

# NetBeans

* [nbts](https://github.com/Everlaw/nbts) - NetBeans TypeScript editor plugin

# Notepad++

* [notepadplus-typescript](https://github.com/chai2010/notepadplus-typescript) - notepad++ support TypeScript (Windows).

# Sublime Text

The [TypeScript Plugin for Sublime](https://github.com/Microsoft/TypeScript-Sublime-Plugin), available through [Package Control](https://packagecontrol.io/) for both Sublime Text 3 and Sublime Text 2.

# Vim

### Syntax highlighting

* [leafgarland/typescript-vim](https://github.com/leafgarland/typescript-vim) provides syntax files for highlighting `.ts` and `.d.ts` files.
* [HerringtonDarkholme/yats.vim](https://github.com/HerringtonDarkholme/yats.vim) provides more syntax highlighting and DOM keywords.

### Language Service Tools

* [Quramy/tsuquyomi](https://github.com/Quramy/tsuquyomi)

If you would like to have as-you-type completion, you can install [YouCompleteMe](https://github.com/Valloric/YouCompleteMe) and add the following code into your `.vimrc` to specify what token will trigger the completion. YouCompleteMe will call into its respective TypeScript Plugin for semantic queries.

```vimscript
if !exists("g:ycm_semantic_triggers")
  let g:ycm_semantic_triggers = {}
endif
let g:ycm_semantic_triggers['typescript'] = ['.']
```

# Visual Studio 2013/2015

[Visual Studio](https://www.visualstudio.com/) comes with TypeScript when installing Microsoft Web Tools.

TypeScript for Visual Studio 2015 can be found [here] (http://www.microsoft.com/en-us/download/details.aspx?id=48593)

TypeScript for Visual Studio 2013 can be found [here] (https://www.microsoft.com/en-us/download/details.aspx?id=48739)

# Visual Studio Code

[Visual Studio Code](https://code.visualstudio.com/), a lightweight cross-platform editor, comes with TypeScript support built in.

# Webstorm

[WebStorm](https://www.jetbrains.com/webstorm/), as well as other JetBrains IDEs, contain TypeScript language support out of the box.
