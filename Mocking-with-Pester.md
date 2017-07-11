Pester provides a set of Mocking functions making it easy to fake dependencies 
and also to verify behavior. Using these mocking functions can allow you to 
"shim" a data layer or mock other complex functions that already have their 
own tests.

DESCRIPTION
------------
With the set of Mocking functions that Pester exposes, one can:

* Mock the behavior of ANY powershell command.
* Verify that specific commands were (or were not) called.
* Verify the number of times a command was called with a set of specified parameters.

MOCKING FUNCTIONS
-------------------
### Mock
Mocks the behavior of an existing command with an alternate implementation.

### Assert-VerifiableMocks
Checks if any Verifiable Mock has not been invoked. If so, this will throw an exception.

### Assert-MockCalled
Checks if a Mocked command has been called a certain number of times and throws an exception if it has not.

EXAMPLE
--------

```posh
function Build ($version) {
    Write-Host "a build was run for version: $version"
}

function BuildIfChanged {
	$thisVersion = Get-Version
	$nextVersion = Get-NextVersion
	if ($thisVersion -ne $nextVersion) { Build $nextVersion }
	return $nextVersion
}

$here = Split-Path -Parent $MyInvocation.MyCommand.Path
$sut = (Split-Path -Leaf $MyInvocation.MyCommand.Path) -replace '\.Tests\.', '.'
. "$here\$sut"

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
	        $result | Should Be 1.2
	    }
    }
    Context "When there are no Changes" {
    	Mock Get-Version { return 1.1 }
    	Mock Get-NextVersion { return 1.1 }
    	Mock Build {}

    	$result = BuildIfChanged

	    It "Should not build the next version" {
	        Assert-MockCalled Build -Times 0 -ParameterFilter {$version -eq 1.1}
	    }
    }
}
```

---
If you need to mock calls to commands which are made from inside a Script Module, additional code is required.  For details, refer to [Unit Testing within Modules](https://github.com/pester/Pester/wiki/Unit-Testing-within-Modules)

---

MOCKING A FUNCTION THAT IS CALL BY A METHOD IN A POWERSHELL CLASS

Author of an explanation: Dave Wyatt

> PowerShll 5.x is caching class definitions in such a way that the first module to load the class would be the active one, even if you removed the module or changed the class. This totally screws with Pester's Mock command, as it can't find the proper scope to inject the mock. My recommendation at the time was to run your Pester tests in a fresh session every time; this is simple to do with Start-Job. I have this proxy function in my PowerShell profile to help with that:

```
function Invoke-PesterJob
{
[CmdletBinding(DefaultParameterSetName='LegacyOutputXml')]
    param(
        [Parameter(Position=0)]
        [Alias('Path','relative_path')]
        [System.Object[]]
        ${Script},

        [Parameter(Position=1)]
        [Alias('Name')]
        [string[]]
        ${TestName},

        [Parameter(Position=2)]
        [switch]
        ${EnableExit},

        [Parameter(ParameterSetName='LegacyOutputXml', Position=3)]
        [string]
        ${OutputXml},

        [Parameter(Position=4)]
        [Alias('Tags')]
        [string[]]
        ${Tag},

        [string[]]
        ${ExcludeTag},

        [switch]
        ${PassThru},

        [System.Object[]]
        ${CodeCoverage},

        [switch]
        ${Strict},

        [Parameter(ParameterSetName='NewOutputSet', Mandatory=$true)]
        [string]
        ${OutputFile},

        [Parameter(ParameterSetName='NewOutputSet', Mandatory=$true)]
        [ValidateSet('LegacyNUnitXml','NUnitXml')]
        [string]
        ${OutputFormat},

        [switch]
        ${Quiet}
    )

    $params = $PSBoundParameters

    Start-Job -ScriptBlock { Set-Location $using:pwd; Invoke-Pester @using:params } |
    Receive-Job -Wait -AutoRemoveJob
}
Set-Alias ipj Invoke-PesterJob

```

The described issue is probably resolved in PowerShell 6.x.

[Source](https://github.com/pester/Pester/issues/797#issuecomment-314495326)