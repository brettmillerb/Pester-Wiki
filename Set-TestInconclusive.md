> This is a command that you can use to neither test nor fail a failure. Instead, Pester will just throw its metaphorical hands up and tell you it doesn't know what's going on. This is a great command to use in pre-testing because if a dependency isn't there before the test runs, the test cannot pass or fail because it won't run in the first place. Set-TestInconclusive is a perfect command to run when a dependency does not exist.

The quote from Adam's Bertram article [Working with infrastructure dependencies in Pester](https://4sysops.com/archives/working-with-infrastructure-dependencies-in-pester).

Set-TestInconclusive was introduced as the respond for [the issue 395](https://github.com/pester/Pester/issues/395)