There are three great ways to contribute to the TypeScript project: logging bugs, submitting pull requests, and creating suggestions.

### Logging Bugs
To log a bug, just use the GitHub issue tracker. Confirmed bugs will be labelled with the `Bug` label. Please include code to reproduce the issue and a description of what you expected to happen.

### Pull Requests
Before we can accept a pull request from you, you'll need to sign the Contributor License Agreement (CLA). See the "Legal" section of the [CONTRIBUTING.md guide](https://github.com/Microsoft/TypeScript/blob/master/CONTRIBUTING.md). That document also outlines the technical nuts and bolts of submitting a pull request. Be sure to follow our [[Coding Guidelines|coding-guidelines]].

### Suggestions
We're also interested in your feedback in future of TypeScript. You can submit a suggestion or feature request through the issue tracker. To make this process more effective, we're asking that these include more information to help define them more clearly. Start by reading the [[TypeScript Design Goals]] and refer to [[Writing Good Design Proposals]] for information on how to write great feature proposals.

### Issue Tracking 101
Unlabelled issues haven't been looked at by a TypeScript coordinator. You can expect to see them labelled within a few days of being logged.

Issues with the `Bug` label are considered to be defects. Once they have the `Bug` label, they'll either be assigned to a TypeScript developer and assigned a milestone, or put in the Community milestone, indicating that we're accepting pull requests for this bug. Community bugs are a great place to start if you're interested in making a code contribution to TypeScript.

We'll be using Labels to track the status of suggestions or feature requests. You can expect to see the following:
 * `Suggestion`: We consider this issue to not be a bug per se, but rather a design change or feature of some sort. Any issue with this label should have at least one more label from the list below
 * `Needs Proposal`: A full write-up is needed to explain how the feature should work
 * `Needs More Info`: A proposal exists, but there are follow-up questions that need to be addressed
 * `In Discussion`: This is being discussed by the TypeScript design team. You can expect this phase to take at least a few weeks, depending on our schedule
 * `Ready to Implement`: The proposal is accepted and has been designed enough that it can be implemented now
 * `Accepting PRs`: We are accepting pull requests that fully implement this feature
 * `Committed`: We have allocated time on the team schedule to implement this feature

Declined suggestions will have the `Declined` label along with one of the following:
 * `Out of Scope`: Is outside the scope of the TypeScript compiler; would be better implemented as a separate tool or extension rather than a change to TypeScript itself
 * `Too Complex`: The amount of complexity that this (and its future implications) would introduce is not justified by the amount of value it adds to the language
 * `Breaking Change`: Would meaningfully break compatibility with JavaScript or a previous version of TypeScript, or would prevent us from implementing known future ECMAScript proposals
 * `By Design`: This aspect of the language is an intentional design decision

Issues that are not bugs or suggestions will be labelled appropriately (`Question`, `By Design`, `External`) and closed. Please use [Stack Overflow](http://stackoverflow.com/questions/tagged/typescript) for TypeScript questions.

### Documentation

For any new features, please:
* Add a link to the Roadmap: https://github.com/Microsoft/TypeScript/wiki/Roadmap
* Add a blurb to what's new page: https://github.com/Microsoft/TypeScript/wiki/What%27s-new-in-TypeScript
* Add a section to the Handbook, if big enough:  https://github.com/Microsoft/TypeScript-Handbook
* For breaking changes:
 * Add a breaking change notice: https://github.com/Microsoft/TypeScript/wiki/Breaking-Changes
 * or to the API breaking changes pages: https://github.com/Microsoft/TypeScript/wiki/API-Breaking-Changes
