## The TypeScript wiki

This repo is the source of truth for the GitHub wiki, PRs to this repo are automatically merged into the 
auto-generated GitHub [repo for the wiki](https://github.com/microsoft/TypeScript.wiki.git). This is done 
in a GitHub Action in [`.github/workflows/sync.yml`](.github/workflows/sync.yml).

The wiki root is [Home.md](./Home.md).

You can run this locally if you have ruby installed via:

```sh
# Install the deps
gem install gollum

# Start the server
gollum
```

Then you can open: `http://localhost:4567`

Things to remember:

- Wikis don't support nesting, so filenames have to get a bit wild

  ```diff
  - compiler/testing/fourslash.md
  + compiler-testing-fourslash.md
  ```

- You can use a custom link syntax for references to TypeScript code which will
  be looked up at deploy time:

  ```
  link to [`runFourSlashTest`][0]

  [0]: <src/harness/fourslash.ts - function runFourSlashTest(>
  ```

  Will look at the file `src/harness/fourslash.ts` in microsoft/TypeScript to 
  find the line of code `function runFourSlashTest` and provide a direct link 
  in the wiki. You can audit them via the script `npm run lint`.

# Contributing

This project welcomes contributions and suggestions.  Most contributions require you to agree to a
Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us
the rights to use your contribution. For details, visit https://cla.microsoft.com.

When you submit a pull request, a CLA-bot will automatically determine whether you need to provide
a CLA and decorate the PR appropriately (e.g., label, comment). Simply follow the instructions
provided by the bot. You will only need to do this once across all repos using our CLA.

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/).
For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or
contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.

# Legal Notices

Microsoft and any contributors grant you a license to the Microsoft documentation and other content in this repository under the [Creative Commons Attribution 4.0 International Public License](https://creativecommons.org/licenses/by/4.0/legalcode), see the [LICENSE](LICENSE) file, and grant you a license to any code in the repository under the [MIT License](https://opensource.org/licenses/MIT), see the [LICENSE-CODE](LICENSE-CODE) file.

Microsoft, Windows, Microsoft Azure and/or other Microsoft products and services referenced in the documentation may be either trademarks or registered trademarks of Microsoft in the United States and/or other countries.
The licenses for this project do not grant you rights to use any Microsoft names, logos, or trademarks.
Microsoft's general trademark guidelines can be found at http://go.microsoft.com/fwlink/?LinkID=254653.

Privacy information can be found at https://privacy.microsoft.com/en-us/

Microsoft and any contributors reserve all other rights, whether under their respective copyrights, patents, or trademarks, whether by implication, estoppel or otherwise.
