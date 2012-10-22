Invokes Pester to run all tests (files containing .Tests.) recursively under the relative_path

DESCRIPTION
------------
Upon calling Invoke-Pester. All files that have a name containing 
".Tests." will have there tests defined in their Describe blocks 
executed. Invoke-Pester begins at the location of relative_path and 
runs recursively through each sub directory looking for 
\*.Tests.\* files for tests to run. If a TestName is provided, 
Invoke-Pester will only run tests that have a describe block with a 
matching name. By default, Invoke-Pester will end the test run with a 
simple report of the number of tests passed and failed output to the 
console. One may want pester to "fail a build" in the event that any 
tests fail. To accomodate this, Invoke-Pester will return an exit 
code equal to the number of failed tests if the EnableExit switch is 
set. Invoke-Pester will also write a NUnit style log of test results 
if the OutputXml parameter is provided. In these cases, Invoke-Pester 
will write the result log to the path provided in the OutputXml 
parameter.

PARAMETER 
----------
###relative_path
The path where Invoke-Pester begins to search for test files. The default is the current directory.

###testName
Informs Invoke-Pester to only run Describe blocks that match this name.

###EnableExit
Will cause Invoke-Pester to exit with a exit code equal to the number of failed tests once all tests have been run. Use this to "fail" a build when any tests fail.

###OutputXml
The path where Invoke-Pester will save a NUnit formatted test results log file. If this path is not provided, no log will be generated.

Example 1
---------
    Invoke-Pester

This will find all \*.tests.\* files and run their tests. No exit code will be returned and no log file will be saved.

Example 2
-----------

    Invoke-Pester ./tests/Utils*

This will run all tests in files under ./Tests that begin with Utils and alsocontains .Tests.

Example 3
-----------

    Invoke-Pester -TestName "Add Numbers"

This will only run the Describe block named "Add Numbers"

Example 4
------------

    Invoke-Pester -EnableExit -OutputXml "./artifacts/TestResults.xml"

This runs all tests from the current directory downwards and writes the results according to the NUnit schema to artifatcs/TestResults.xml just below the current directory. The test run will return an exit code equal to the number of test failures.
