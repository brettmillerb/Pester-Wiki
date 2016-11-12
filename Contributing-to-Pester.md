# Giving Back to Pester
To propose a new feature to Pester or report a bug, we encourage you to first share your findings with us by creating a new issue.  The feature or bug will be discussed, and if it's something we would like (or even love!) to see in our codebase we will ask you to create a pull request (PR) for it. We have a (more or less) standardized process for accepting PRs, but don't let that scare you away. We understand that you spent your free time to contribute, so we will take our time to help you successfully add your code to Pester.

## Reporting a bug
Your bug report should include the description of what is wrong, the version of Pester, PowerShell and the operating system. To make your bug report perfect you should also include a simple way to reproduce the bug.

Here is a piece of code that collects the required system information for you and puts it in your clipboard:
```powershell
$bugReport = &{
    $p = get-module pester 
    "Pester version     : " + $p.Version + " " + $p.Path 
    "PowerShell version : " + $PSVersionTable.PSVersion
    "OS version         : " + [System.Environment]::OSVersion.VersionString
}
$bugReport
$bugReport | clip
```

Example output:
```
Pester version     : 3.4.4 C:\Users\nohwnd\Documents\GitHub\Pester_main\Pester.psm1
PowerShell version : 5.1.14393.206
OS version         : Microsoft Windows NT 10.0.14393.0
```

The best way to report the reproduction steps is in a form of a Pester test. But it's not always easy to do, especially if you are reporting a bug in some internal part of the framework. So feel free to provide just a list of steps that need to be taken to reproduce the bug.

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