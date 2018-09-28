Hello,
I want to mock variable assignment using Set-Variable and I'm trying to do something like this

function Test-Variable {
    Param()

    Set-Variable -Name 'Variable1' -Value "Test 01"
    if ($null -eq $Variable1 -or $Variable1 -eq [string]::Empty) {
        return "Varaible 1 missing"
    }

    Set-Variable -Name 'Variable2' -Value "Test 02"
    if ($null -eq $Variable2 -or $Variable2 -eq [string]::Empty) {
        return "Varaible 2 missing"
    }
}

Describe 'Test-Variable' {

    Context "Mock Variable1" {
        Mock -CommandName 'Set-Variable' -MockWith {return $null} -Verifiable -ParameterFilter {$Name -and $Name -eq 'Variable1'}

        it 'Mock Variable1' {
            $Result = Test-Variable
            $Result | Should -Contain "Varaible 1 missing"
           
            Assert-MockCalled -CommandName Set-Variable -Times 1 -Exactly -ParameterFilter {$Name -eq 'Variable1'}
        }
    }

    Context "Mock Variable2" {
        Mock -CommandName 'Set-Variable' -MockWith {return $null} -Verifiable -ParameterFilter {$Name -and $Name -eq 'Variable2'}

        it 'Mock Variable2' {
            $Result = Test-Variable
            $Result | Should -Contain "Varaible 2 missing"
           
            Assert-MockCalled -CommandName Set-Variable -Times 1 -Exactly -ParameterFilter {$Name -eq 'Variable2'}
        }
    }
}

Describing Test-Variable

  Context Mock Variable1
    [+] Mock Variable1 183ms

  Context Mock Variable2
    [-] Mock Variable2 125ms
      Expected 'Varaible 2 missing' to be found in collection Varaible 1 missing, but it was not found.
      19:             $Result | Should -Contain "Varaible 2 missing"


I don't know if it's a bug or I'm doing something wrong ?
FYI
PS C:\> Get-Module -Name Pester

ModuleType Version    Name                                ExportedCommands
---------- -------    ----                                ----------------
Script     4.4.0      Pester                              {Add-AssertionOperator, AfterAll, AfterEach, AfterEachFeature...}
PS C:\> $PSVersionTable

Name                           Value
----                           -----
PSVersion                      5.1.14409.1012
PSEdition                      Desktop
PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0...}
BuildVersion                   10.0.14409.1012
CLRVersion                     4.0.30319.42000
WSManStackVersion              3.0
PSRemotingProtocolVersion      2.3
SerializationVersion           1.1.0.1

Thx