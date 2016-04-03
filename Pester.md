Pester is a BDD based test runner for PowerShell.

Pester provides a framework for running Unit Tests to execute and validate 
PowerShell commands. Pester follows a file naming convention for naming 
tests to be discovered by pester at test time and a simple set of 
functions that expose a Testing DSL for isolating, running, evaluating and 
reporting the results of PowerShell commands.

Pester tests can execute any command or script that is accessible to a 
pester test file. This can include functions, Cmdlets, Modules and scripts. 
Pester can be run in ad hoc style in a console or it can be integrated into 
the Build scripts of a Continuous Integration system.

Pester also contains a powerful set of Mocking Functions that allow tests to 
mimic and mock the functionality of any command inside of a piece of 
PowerShell code being tested. See [[Mocking with Pester]].

CREATING A PESTER TEST
------------------------
To start using Pester, you may use the [[New-Fixture]] function to scaffold both 
a new implementation function and a test function. It is not necessary to use
New-Fixture, but it's there if you want it. New-Fixture assumes that you will
be testing the contents of a ```.ps1``` file (not a ```.psm1``` file, or Script Module), that
the test script should be in the same directory as the script under test, and that
the name of the test script should be ```<Name of Script Under Test>.Tests.ps1```. 

If you wish to create script files manually with different conventions, that's fine, but all
Pester test scripts _must_ end with ```.Tests.ps1``` in order for [[Invoke-Pester]] to run them.

```posh
New-Fixture deploy Clean

<#
Creates two files:
./deploy/Clean.ps1
#>

function Clean {

}

# ./deploy/Clean.Tests.ps1

$here = Split-Path -Parent $MyInvocation.MyCommand.Path
$sut = (Split-Path -Leaf $MyInvocation.MyCommand.Path) -replace '\.Tests\.', '.'
. "$here\$sut"

Describe "Clean" {
    It "does something useful" {
        $true | Should Be $false
    }
}
```

Now you have a skeleton of a Clean function with a failing test. Pester 
considers all files with names ending in ```.Tests.ps1``` to be a test file (see 
[[Invoke-Pester]]) and by default it will look for these files in the current directory 
and run all Describe blocks inside the file (see [[Describe]]). 

The Describe block can 
contain several behavior validations expressed in It blocks (see [[It]]). 
Each It block should test one thing and throw an exception if the test 
fails. Pester will consider any It block that throws an exception to be a 
failed test. Pester provides a command called Should that can perform various 
comparisons between the values emitted or altered by a command and an expected 
value (see [[Should]]). 

RUNNING A PESTER TEST
-----------------------
Once you have some logic that you are ready to test, use the Invoke-Pester 
command to run the tests (see [[Invoke-Pester]]). You run all the test files in an 
entire tree of directories or zero in on just one test (Describe block)
using ```-TestName``` parameter.

As of Pester version 3.0, you can also directly execute any ```.Tests.ps1``` script file;
however, by doing so, you will only see test output at the console.  You give up
any of the benefits of using Invoke-Pester, such as the ability to produce NUnit
XML files or output objects, Code Coverage analysis, an exit code from PowerShell.exe,
filtering of tests to execute, and a status summary of tests executed / failed
when the run is complete.

PESTER AND CONTINUOUS INTEGRATION
------------------------------------
Pester integrates well with almost any build automation solution. You 
could create a MSBuild target that calls Pester's convenience Batch file:

	<Target Name="Tests">
	<Exec Command="cmd /c $(baseDir)pester\bin\pester.bat" />
	</Target>

This will start a PowerShell session, import the Pester Module and call 
invoke pester within the current directory. If any test fails, it will 
return an exit code equal to the number of failed tests and all test 
results will be saved to Test.xml using NUnit's Schema allowing you to 
plug these results nicely into most Build systems like CruiseControl, 
TeamCity, TFS or Jenkins.

OTHER EXAMPLES
-----------------
- Pester's own tests. See all files in the Pester Functions folder containing ```.Tests.ps1```
- Chocolatey tests. Chocolatey is a popular PowerShell-based Windows 
package management system. It uses Pester tests to validate its own 
functionality.