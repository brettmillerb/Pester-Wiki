There may be times when Pester does not meet all of your needs either in the form of new features or bug fixes. In these cases, Pester has two ways that you can communicate these needs to use through either creating an Issue or submitting a pull request.


# Giving Back to Pester through Pull Requests

## Proposing a New Function

In order to propose a new function to be added to Pester, we ask that you:

1. Create a PS1 script with $FunctionName in the Functions directory.
2. Add your function to the FunctionsToExport key in the Pester module manifest.
3. Create a Pester test file in the form of $FunctionName.Tests.ps1 in the Functions directory.
4. Do not dot source the function script in your tests. The function will already be included as part of the module.
5. Ensure no trailing whitespace is included in your function or in your test code.
6. Ensure your code works on PowerShell versions 2-5.

Below is a template that can be used:

### Functions\New-Function.ps1
```
function New-Function {
    <#
    .SYNOPSIS
        SYNOPSIS here.

    .PARAMETER Parameter
        <Parameter explanation>

    .EXAMPLE
        PS> $obj = New-Function -Parameter 'Foo'
    #>

    param (
        [Parameter(Mandatory = $true)]
        [ValidateNotNullOrEmpty()]
        [string]$Paraemeter
    )

    ## Function code here

}
```

### Functions\New-Function.Tests.ps1
```
describe 'New-Function' {

    it 'does something' {
        New-Function -Parameter 'Foo' | should be 'bar'
    }
}
```

### Pester.psd1
```
FunctionsToExport = @( 
    'Describe',
    'Context',
    'It',
    'Should',
    'Mock',
    'Assert-MockCalled',
    'Assert-VerifiableMocks',
    'New-Fixture',
    'Get-TestDriveItem',
    'Invoke-Pester',
    'Setup',
    'In',
    'InModuleScope',
    'Invoke-Mock',
    'BeforeEach',
    'AfterEach',
    'BeforeAll',
    'AfterAll'
    'Get-MockDynamicParameters',
    'Set-DynamicParameterVariables',
    'Set-TestInconclusive',
    'SafeGetCommand',
    'New-PesterOption',
    'New-Function'
)
```