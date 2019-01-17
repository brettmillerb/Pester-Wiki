Creates an object that contains advanced options for Invoke-Pester.

PARAMETERS
----------
### IncludeVSCodeMarker
When this switch is set, an extra line of output will be written to the console for test failures, making it easier for VSCode's parser to provide highlighting / tooltips on the line where the error occurred.

### TestSuiteName
When generating NUnit XML output, this controls the name assigned to the root "test-suite" element.  Defaults to "Pester".

### Experimental
Enables experimental features of Pester to be enabled.

### ShowScopeHints
EXPERMINETAL: Enables debugging output for debugging tranisition among scopes. (Experimental flag needs to be used to enable this.)

Example
---------
    $Options = New-PesterOption -TestSuiteName "Tests - Set A"

    Invoke-Pester -PesterOption $Options -Outputfile ".\Results-Set-A.xml" -OutputFormat NUnitXML

    The result of commands will be execution of tests and saving results of them in a NUnitMXL file
    where the root "test-suite" will be named "Tests - Set A".
