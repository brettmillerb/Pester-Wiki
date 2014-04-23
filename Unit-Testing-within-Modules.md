## Introduction
If you have code like this inside a a PS module (.psm1 file):
````powershell
function BuildIfChanged {
    $thisVersion=Get-Version
    $nextVersion=Get-NextVersion
    if($thisVersion -ne $nextVersion) {Build $nextVersion}
    return $nextVersion
}

function Build ($version) {
           write-host "a build was run for version: $version"
}
#...other functions not included

Export-ModuleMember BuildIfChanged
````

The "problem" here is that the only function that's exposed in the public space so to say is the *BuildIfChanged* function. BUT, we need to be able to mock the other functions to be able to write tests like this:
````powershell
Describe "BuildIfChanged" {
    Context "When there are Changes" {
        Mock Get-Version {return 1.1}
        Mock Get-NextVersion {return 1.2}
        Mock Build {} -Verifiable -ParameterFilter {$version -eq 1.2}

        $result = BuildIfChanged

        It "Builds the next version" {
            Assert-VerifiableMocks
        }
        It "returns the next version number" {
            $result.Should.Be(1.2)
        }
    }
    Context "When there are no Changes" {
        Mock Get-Version -MockWith {return 1.1}
        Mock Get-NextVersion -MockWith {return 1.1}
        Mock Build -MockWith {}

        $result = BuildIfChanged

        It "Should not build the next version" {
            Assert-MockCalled Build -Times 0 -ParameterFilter{$version -eq 1.1}
        }
    }
}
````

Oh, and btw we don't want to/can extract those 'helper' functions into their own files (which could be a solution).


## Solution/Workaround
A simple workaround is to "fake" dot sourcing...which doesn't work for .psm1 files OOB with PowerShell. 
At the top of the test file we’ll put this snippet of code:

````powershell
$module = (Split-Path -Leaf $PSCommandPath).Replace(".Tests.ps1", ".psm1")
$code = Get-Content $module | Out-String
Invoke-Expression $code
````

**Downsides?**
Yes, there’s one at least and that is you can have trouble debugging since you’re no longer using the “real” module but just the contents of it so to say. I haven’t found a way to debug code that has been put into a script using Invoke-Expression at least.

##Resources
* http://johanleino.wordpress.com/2013/09/25/pester-how-to-unit-test-your-powershell-modules/
