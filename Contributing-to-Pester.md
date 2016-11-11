# Giving Back to Pester
To propose a new feature to Pester or report a bug, we encourage you to first share your findings with us by creating a new issue.  The feature or bug will be discussed, and if it's something we would like (or even love!) to see in our codebase we will ask you to create a pull request (PR) for it. We have a (more or less) standardized process for accepting PRs, but don't let that scare you away. We understand that you spent your free time to contribute, so we will take our time to help you successfully add your code to Pester.

## Reporting a bug





## Proposing a new Function

In order to propose a new function to be added to Pester, we ask that you:

1. Fork the Pester repo.
2. Create a PS1 script with $FunctionName in the Functions directory.
3. Add your function to the FunctionsToExport key in the Pester module manifest.
4. Add the function to exported commands in the Pester module
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