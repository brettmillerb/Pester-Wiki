TestDrive is a PSDrive for file activity limited to the scope of a single Describe or Context block.

A test may need to work with file operations and validate certain types of file activities. It is usually desirable not to perform file activity tests that will produce side effects outside of an individual test. Pester creates a PSDrive inside the user's temporary drive that is accessible via a names PSDrive TestDrive:. Pester will remove this drive after the test completes. You may use this drive to isolate the file operations of your test to a temporary store.

EXAMPLE
--------

```posh
function Add-Footer($path, $footer) {
	Add-Content $path -Value $footer
}

Describe "Add-Footer" {
	$testPath = "TestDrive:\test.txt"
	Set-Content $testPath -value "my test text."
	Add-Footer $testPath "-Footer"
	$result = Get-Content $testPath

	It "adds a footer" {
	    (-join $result) | Should Be "my test text.-Footer"
	}
}
```

When this test completes, the contents of the TestDrive PSDrive will be removed.

Compare with literal path
---------
Use the $TestDrive variable to compare regular paths with TestDrive paths.  The following
two paths will refer to the same file on disk, but the first one will contain the full file
system path to the root of the TestDrive PSDrive, rather than a PowerShell path starting with
'TestDrive:\'.

```posh
$pathOne = Join-Path $TestDrive 'somefile.txt'
$pathTwo = 'TestDrive:\somefile.txt'
```
