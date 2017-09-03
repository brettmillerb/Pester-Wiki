## Compatibility
Pester is compatible with every version of Windows that can run at least PowerShell 2. That is Windows 10, 8, 7, Vista, XP, and their respective Server versions 2016, 2012 R2, 2012, 2008 R2, 2008, and 2003.


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


## Installing and updating on earlier versions of Windows


The way you can install a new module differs based on the version of PowerShell you are using. See what version of PowerShell is installed on your computer by running:

```powershell
"$($PSVersionTable.PSVersion)"
```

# PowerShell 5 or newer
In version 5 or later you can use the built-in `Install-Module` and Update-Module` cmdlets coming from [PowerShellGet](https://github.com/PowerShell/PowerShellGet).

From _administrator_ PowerShell command line run:
```powershell
Install-Module -Name Pester
```
Or to update:
```powershell
Update-Module -Name Pester
```

# PowerShell 3 or newer
Install PowerShell get?

Install PSGet?

# PowerShell 2
Install PSGet ?


### Installing from Chocolatey

### Installing from Nuget

### Getting the sources directly


### tbd 
moved from readme: todo: add page with all the info for installation. most likely we will split it by versions of PowerShell, not by windows. But I am using compatibility with windows in here , how to install, update, why those steps are needed, and how to remove the old Pester version from windows.
Do not use the `-Scope User` parameter.
