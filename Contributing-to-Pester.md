There may be times when Pester does not meet all of your needs either in the form of new features or bug fixes. In these cases, Pester has two ways that you can communicate these needs to use through either creating an Issue or submitting a pull request.


# Giving Back to Pester through Pull Requests

## Proposing a New Function

In order to propose a new function to be added to Pester, we ask that you:

1. Fork the Pester repo.
2. Create a PS1 script with $FunctionName in the Functions directory.
3. Add your function to the FunctionsToExport key in the Pester module manifest.
4. Add the function to SafeCommands in the Pester module
   `& $script:SafeCommands['Export-ModuleMember'] New-Function`
5. Create a Pester test file in the form of $FunctionName.Tests.ps1 in the Functions directory.
   - Do not dot source the function script in your tests. The function will already be included as part of the module.
   - Ensure your code works on PowerShell versions 2-5.
   - Run the Pester test suite.
````
    Get-Module Pester | Remove-Module 
    Import-Module .\Pester.psd1
    Invoke-Pester -Path 'C:\Program Files\WindowsPowerShell\Modules\Pester\Functions'
````
6. Commit the change.
7. Submit a pull request.