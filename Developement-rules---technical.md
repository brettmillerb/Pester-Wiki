Due that PowerShell is now available for operating system different than Windows, please
- use [System.Environment]::NewLine instead `` `n`` or `\n`

If you share your local repository folder between Windows and Linux (e.g. by mounting/sharing it to a virtual machine) please set in the repository settings ```filemode = false``` - it needs to be done in the file .git\config.
