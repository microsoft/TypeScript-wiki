# Quick List
 * [Atom](#Atom)
 * [Eclipse](#Eclipse)
 * [Emacs](#Emacs)
 * [NetBeans](#NetBeans)
 * [Sublime Text](#Sublime-Text)
 * [Vim](#Vim)
 * [Visual Studio](#visual-studio-20132015)
 * [Visual Studio Code](#visual-studio-code)

# Atom
[Atom-TypeScript](https://atom.io/packages/atom-typescript), a TypeScript language service for Atom developed by TypeStrong

# Eclipse

[Eclipse TypeScript Plug-in](https://github.com/palantir/eclipse-typescript), an Eclipse plugin developed by Palantir.

# Emacs

[tide](https://github.com/ananthakumaran/tide) - TypeScript Interactive Development Environment for Emacs

# NetBeans

* [nbts](https://github.com/Everlaw/nbts) - NetBeans TypeScript editor plugin
* [Geertjan's TypeScript NetBeans Plugin](https://github.com/GeertjanWielenga/TypeScript)

# Sublime Text

The [TypeScript Plugin for Sublime](https://github.com/Microsoft/TypeScript-Sublime-Plugin), available through [Package Control](https://packagecontrol.io/) for both Sublime Text 3 and Sublime Text 2.

# Vim

### Syntax highlighting

* [leafgarland/typescript-vim](https://github.com/leafgarland/typescript-vim) provides syntax files for highlighting `.ts` and `.d.ts` files.
* [HerringtonDarkholme/yats.vim](https://github.com/HerringtonDarkholme/yats.vim) provides more syntax highlighting and DOM keywords.

### Language Service Tools

There are two main TypeScript plugins:

* [Quramy/tsuquyomi](https://github.com/Quramy/tsuquyomi)
* [clausreinke/typescript-tools.vim](https://github.com/clausreinke/typescript-tools.vim)

If you would like to have as-you-type completion, you can install [YouCompleteMe](https://github.com/Valloric/YouCompleteMe) and add the following code into your `.vimrc` to specify what token will trigger the completion. YouCompleteMe will call into its respective TypeScript Plugin for semantic queries.

```vimscript
if !exists("g:ycm_semantic_triggers")
  let g:ycm_semantic_triggers = {}
endif
let g:ycm_semantic_triggers['typescript'] = ['.']
```

# Visual Studio 2013/2015

[Visual Studio](https://www.visualstudio.com/) comes with TypeScript when installing Microsoft Web Tools.

# Visual Studio Code

[Visual Studio Code](https://code.visualstudio.com/), a lightweight cross-platform editor, comes with TypeScript support built in.
