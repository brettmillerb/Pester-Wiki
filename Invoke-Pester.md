Executes all Pester tests found in `*.Tests.ps1` files, and provides various
options for producing test output or generating metrics during execution.

DESCRIPTION
------------
Upon calling Invoke-Pester. All files that have a name containing 
`.Tests.` will have the tests defined in their Describe blocks 
executed. Invoke-Pester begins at the location of relative_path and 
runs recursively through each sub directory looking for 
\*.Tests.\* files for tests to run. If a TestName is provided, 
Invoke-Pester will only run tests that have a describe block with a 
matching name. By default, Invoke-Pester will end the test run with a 
simple report of the number of tests passed and failed output to the 
console. 

One may want pester to "fail a build" in the event that any 
tests fail. To accomodate this, Invoke-Pester will return an exit 
code equal to the number of failed tests if the EnableExit switch is 
set. Invoke-Pester will also write a NUnit style log of test results 
if the `-OutputFormat NUnitXml` parameter is provided. In these cases, Invoke-Pester 
will write the result log to the path provided in the `-OutputFile`
parameter.

PARAMETER 
----------
###Path
The path where Invoke-Pester begins to search for test files. The default is the current directory.

###TestName
Informs Invoke-Pester to only run Describe blocks that match this name.  This value may contain wildcards.

###Tag
Another way of filtering the Describe blocks that should be executed, this time based on the values that are passed to the Tag parameter of the Describe statement.  This value may not contain wildcards.

###EnableExit
Will cause Invoke-Pester to exit with a exit code equal to the number of failed tests once all tests have been run. Use this to "fail" a build when any tests fail.

###OutputFile
The path where Invoke-Pester will save formatted test results log file. If this path is not provided, no log will be generated.

###OutputFormat
The format of output, i.e. `NUnitXml`

###PassThru
Causes Invoke-Pester to produce an output object which can be analyzed by its caller, instead of only sending output to the console.  This can be used as part of a Continuous Integration written in PowerShell, as opposed to relying on a program to read the NUnit xml files produced when the `-OutputFormat` parameter is used.

The object produced by Invoke-Pester when the PassThru switch is used contains the following properties:

- Path:  The path in which tests were found and run.
- TagFilter:  The value that was passed to the -Tag parameter, if present.
- TestNameFilter:  The value that was passed to the -TestName parameter, if present.
- TotalCount:  Number of tests executed. 
- PassedCount:  Number of passed tests
- FailedCount:  Number of failed tests.
- Time:  Total time of test execution.
- TestResult:  An array of individual test results, which are themselves objects containing the name of the Describe, Context and It, a Passed flag indicating whether the test passed or failed, a Time value for this test, a FailureMessage (if the test failed), and a stack trace.
- CodeCoverage:  If the -CodeCoverage parameter was also passed to Invoke-Pester, the output object will contain a CodeCoverage property as well.

###CodeCoverage
Causes Pester to produce a report of code coverage metrics while the tests are executing.  For more details, refer to the [[Code Coverage]] section of this wiki.

###Script
A hashtable used to pass parameters into your tests among other options.

###Strict
Reduces the possible outcome of a test to Passed or Failed only. Any Pending or Skipped test will translate to Failed (see [[It]]). This is useful for running tests as a part of continuos integration, where you need to make sure that all tests passed, and no tests were skipped or pending.


Example 1
---------
    Invoke-Pester

This will find all \*.tests.\* files and run their tests. No exit code will be returned and no log file will be saved.

Example 2
-----------

    Invoke-Pester ./tests/Utils*

This will run all tests in files under ./Tests that begin with Utils and also contains .Tests.

Example 3
-----------

    Invoke-Pester -TestName "Add Numbers"

This will only run the Describe block named "Add Numbers"

Example 4
------------

    Invoke-Pester -EnableExit -OutputFile "./artifacts/TestResults.xml" -OutputFormat NUnitXml

This runs all tests from the current directory downwards and writes the results according to the NUnit schema to artifatcs/TestResults.xml just below the current directory. The test run will return an exit code equal to the number of test failures.

Example 5
------------

    $result = Invoke-Pester -PassThru

This saves an object containing the results of the tests to the $result variable.  This can be analyzed as part of a Continuous Integration solution developed in PowerShell.