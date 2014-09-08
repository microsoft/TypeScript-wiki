When a spec change is checked in (for example [#589](https://github.com/Microsoft/TypeScript/pull/589)), it is necessary to ensure thorough test coverage for all the areas that have changed. In other words, all changes in the spec should be observable somewhere in the tests/cases/conformance folder. The following process explains how to ensure this coverage.

For each spec change:

1. Try to find a bug for the spec change. Also look at any pull requests associated with that bug.

1. If there is a pull request that changed the compiler’s behavior, inspect the tests that were added as part of that pull request. If they look fitting, move them to the appropriate spot in the conformance suite. Update the baselines (the only change should be the path of the test they reference).

1. If there is no pull request or no bug, try to find a test in [`tests/cases/conformance`](https://github.com/Microsoft/TypeScript/tree/master/tests/cases/conformance/) that exercises this scenario. If there is, verify that the baseline agrees with the spec.

1. If there is no test in conformance, but there is a test in compiler that looks suitable, move it into conformance, and again verify that the baseline agrees with the spec.

1. If there is no existing test, or you’re unable to find one, write a test in the conformance suite. Then verify that the baselines match the spec.

1. If in any of the steps above, the baseline did not match the spec, try to find a bug that says to change the compiler or to change the spec. Make sure that bug is up to date, and add the test info to the bug. If it is closed, reopen it.

1. If there is no bug, find out if the spec or the implementation should be changed, and log a bug for it.

When this process is complete, send a pull request for any tests you have added. Check it in as a normal code change (an example pull request is [#621](https://github.com/Microsoft/TypeScript/pull/621)). Make sure to reference the spec change pull request in your pull request.

Some guidelines and examples for specific test:
* Sometimes if there is a code change associated with a spec change, the tests that have been added along with the code change are sufficient, and merely need to be moved. An example is [`tests/cases/compiler/indexSignatureTypeInference.ts`](https://github.com/Microsoft/TypeScript/tree/master/tests/cases/compiler/indexSignatureTypeInference.ts) in pull request [#196](https://github.com/Microsoft/TypeScript/pull/196). You can see in pull request [#621](https://github.com/Microsoft/TypeScript/pull/621), this file was just moved into the conformance folder.
* Other changes are in broad areas that are very well tested already. For example, the change to best common type (section 3.10) in [#589](https://github.com/Microsoft/TypeScript/pull/589) has an entire folder under [`tests/cases/conformance/types/typeRelationships/bestCommonType`](https://github.com/Microsoft/TypeScript/tree/master/tests/cases/conformance/types/typeRelationships/bestCommonType). To verify that this is sufficient, make sure that every bullet in the changed spec has some code in the test folder that exercises it.
* Lastly, for new sections of the spec, and for sections where we have poor coverage, it may be necessary to add some amount of new test material. An example is the change to type queries (section 3.6.3) in change [#589](https://github.com/Microsoft/TypeScript/pull/589). Take a look at file [`tests/cases/conformance/types/specifyingTypes/typeQueries/recursiveTypesWithTypeof.ts`](https://github.com/Microsoft/TypeScript/blob/specConformanceTests/tests/cases/conformance/types/specifyingTypes/typeQueries/recursiveTypesWithTypeof.ts) in [#621](https://github.com/Microsoft/TypeScript/pull/621) to see an example of adding tests for a change like this. The amount of testing necessary here is based on judgment of how complex the changed area is.