Migration from Pester 3 to 4 typically involves minor changes to test code.  Sometimes the migration requires no changes at all.  This guide is meant to help you understand and make the necessary changes to your existing test code to accommodate Pester 4.

## Update to New Names

We recommend performing string replacement on tests written for Pester 3 and earlier as follows:

1. replace all occurrences of any `Contain` assertion with `FileContentMatch`.
1. rename all occurrences of `Assert-VerifiableMocks` to `Assert-VerifiableMock`
1. rename all occurrences of `Get-MockDynamicParameters` to `Get-MockDynamicParameter`
1. rename all occurrences of `Set-DynamicParameterVariables` to `Set-DynamicParameterVariable`

You can use this simple script that can migrate test files in UTF8 and ASCII encoding:

```powershell
param ($Path = '.')

# version 2017-9-10

function Get-FileEncoding
{
    # source https://gist.github.com/jpoehls/2406504
    # simplified.

    [CmdletBinding()] 
    Param (
        [Parameter(Mandatory = $True, ValueFromPipelineByPropertyName = $True)] 
        [string]$Path
    )

    [byte[]]$byte = get-content -Encoding byte -ReadCount 4 -TotalCount 4 -Path $Path

    if ( $byte[0] -eq 0xef -and $byte[1] -eq 0xbb -and $byte[2] -eq 0xbf )
    { Write-Output 'UTF8' }

    else
    { Write-Output 'ASCII' }
}

$testFiles = Get-ChildItem -Path $Path -Recurse *.Tests.ps1 

foreach ($file in $testFiles)
{
    "Migrating '$file'"
    $encoding = Get-FileEncoding $file
    $content = Get-Content -Path $file -Encoding $encoding
    $content = $content -replace 'Should\s+\-?Contain', 'Should -FileContentMatch'
    $content = $content -replace 'Should\s+\-?Not\s*-?Contain', 'Should -Not -FileContentMatch'
    $content = $content -replace 'Assert-VerifiableMocks', 'Assert-VerifiableMock'
    $content = $content -replace 'Get-MockDynamicParameters', 'Get-MockDynamicParameter'
    $content = $content -replace 'Set-DynamicParameterVariables', 'Set-DynamicParameterVariable'
    $content | Set-Content -Path $file -Encoding $encoding
}
```
## Mock-Related Changes
We are not 100% sure what implications changing from functions to aliases had on mocking. There are no immediate changes that you need to do, but here are two articles that should help you start figuring out issues if you have any:
- [Get-Command of mocked function is less complete](https://github.com/pester/Pester/issues/810)
- [Summary of mock scope changes](https://github.com/pester/Pester/issues/812)

## Array-Related Changes

The `Should` command now performs array assertions.  For the most part this change is transparent to existing tests.  There are some edge cases, however, where a test involving an array passes in Pester 3 but fails in Pester 4.  [This article](https://github.com/pester/Pester/issues/873) contains such an example.