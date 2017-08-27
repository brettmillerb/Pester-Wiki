## Installing on Windows 10 and Windows Server 2016

Windows 10 and Windows Server 2016 make installing and updating PowerShell modules extremely simple by providing `Install-Module` and `Update-Module` cmdlets. Unfortunately there are some complications specific to Pester, that we cannot avoid.

Pester version 3.4.0 ships as part of Windows 10 and Windows server 2016, and that distribution conflicts with the standard module update mechanism. It is not possible to update the built-in Pester to newer version, using the `Update-Module` cmdlet. 

Instead you need to perform a new side-by-side installation of Pester. You are also required to skip publisher check, and force the installation, because `Install-Module` detects the current installation of Pester is signed with a different signature than the one you are installing.

Here's the command you need to run _as administrator_ in order to get the latest version of Pester:

```powershell
Install-Module -Name Pester -Force -SkipPublisherCheck
```

For any subsequent update it is enough to run: 

```powershell
Update-Module -Name Pester
```

## Installing on earlier versions of Windows:


