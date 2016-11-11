There may be times when Pester does not meet all of your needs either in the form of new features or bug fixes. In these cases, Pester has two ways that you can communicate these needs to use through either creating an Issue or submitting a pull request.


# Giving Back to Pester through Pull Requests

## Proposing a New Function

In order to propose a new function to be added to Pester, we ask that you:

1. Create a PS1 script with $FunctionName in the Functions directory.
2. Add your function to the FunctionsToExport key in the Pester module manifest.
3. Add the function to SafeCommands in the Pester module
   `& $script:SafeCommands['Export-ModuleMember'] New-Function`
3. Create a Pester test file in the form of $FunctionName.Tests.ps1 in the Functions directory.
   - Do not dot source the function script in your tests. The function will already be included as part of the module.
   - Run the Pester test suite by running Invoke-Pester with no parameters and correct all problems.
      ```
      Get-Module Pester | Remove-Module 
      Import-Module .\Pester.psd1
      Invoke-Pester -Path (Get-Module Pester).ModuleBase
      ```
6. Ensure your code works on PowerShell versions 2-5.
