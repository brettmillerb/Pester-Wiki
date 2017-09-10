To migrate from Pester 3 to Pester 4, you need to:
1. rename all occurences of any `Contain` assertion for `FileContentMatch`.
1. rename all occurentces of `Assert-VerifiableMocks` to `Assert-VerifiableMock`
1. rename all occurentces of `Get-MockDynamicParameters` to `Get-MockDynamicParameter`
1. rename all occurentces of `Set-DynamicParameterVariables` to `Set-DynamicParameterVariable`

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

mock differences: https://github.com/pester/Pester/issues/812