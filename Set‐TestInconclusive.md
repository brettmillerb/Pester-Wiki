> This is a command that you can use to neither test nor fail a failure. Instead, Pester will just throw its metaphorical hands up and tell you it doesn't know what's going on. This is a great command to use in pre-testing because if a dependency isn't there before the test runs, the test cannot pass or fail because it won't run in the first place. Set-TestInconclusive is a perfect command to run when a dependency does not exist.

The quote from Adam's Bertram article [Working with infrastructure dependencies in Pester](https://4sysops.com/archives/working-with-infrastructure-dependencies-in-pester).


Example of usage

```powershell

Describe "Function1" {
    It "Test what always pass" {

        $true | Should Be $true

        Set-TestInconclusive -Message "I'm inconclusive because I can"
    }

    It "Test what always fail" {

        $true | Should be $false

        Set-TestInconclusive -Message "I'm inconclusive because I can"



    }

    It "Test what is inconclusive" {

        Set-TestInconclusive -Message "I'm inconclusive because I can"

    }
}

```

The function Function1.ps1 is empty (initialized by the New-Fixture).

Returned results are

```powershell
PS >invoke-pester

Executing all tests in <SOME FOLDER>\inconclusive\

Executing script C:\Users\<SOME FOLDER>\inconclusive\Function1.Tests.ps1

  Describing Function1
    [?] Test what always pass 110ms
      I'm inconclusive because I can
      at line: 10 in C:\Users\<SOME FOLDER>\inconclusive\Function1.Tests.ps1
      10:         Set-TestInconclusive -Message "I'm inconclusive because I can"
    [-] Test what always fail 38ms
      Expected: {False}
      But was:  {True}
      at line: 15 in C:\Users\<SOME FOLDER>\inconclusive\Function1.Tests.ps1
      15:         $true | Should be $false
    [?] Test what is inconclusive 96ms
      I'm inconclusive because I can
      at line: 25 in C:\Users\<SOME FOLDER>\inconclusive\Function1.Tests.ps1
      25:         Set-TestInconclusive -Message "I'm inconclusive because I can"Tests completed in 245ms
Tests Passed: 0, Failed: 1, Skipped: 0, Pending: 0, Inconclusive: 2
PS >
```

Explanation 

> Set-TestInconclusive should be the first line of a test, if you want to use it. It blocks are terminated by exceptions, which can be thrown by any command (including Should and Set-TestInconclusive.) Whichever one throws the error first wins.

So Set-TestInconclusive is kind of soft/planned failure for what results are counted as "Inconclusive" result of a test.

Example based on the issue [722](https://github.com/pester/Pester/issues/722).


Set-TestInconclusive was introduced as the respond for the issue [395](https://github.com/pester/Pester/issues/395), the pull request [421](https://github.com/pester/Pester/pull/421).