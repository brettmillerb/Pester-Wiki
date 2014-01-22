Checks if a Mocked command has been called a certain number of times 
and throws an exception if it has not.

## DESCRIPTION

This command checks the call history of the specified Command since 
the Mock was declared. If it had been called less than the number of 
times specified (1 is the default), then an exception is thrown. You 
may specify 0 times if you want to make sure that the mock has NOT 
been called. If you include the Exactly switch, the number of times 
that the command has been called must mach exactly with the number of 
times specified on this command.

## PARAMETERS

### CommandName
The name of the command to check for mock calls.

### Times
The number of times that the mock must be called to avoid an exception 
from throwing.

#### Exactly
If this switch is present, the number specified in Times must match 
exactly the number of times the mock has been called. Otherwise it 
must match "at least" the number of times specified.

### ParameterFilter
An optional filter to qualify which calls should be counted. Only those 
calls to the mock whose parameters cause this filter to return true 
will be counted.

EXAMPLE 1
-----------
````
C:\PS>Mock Set-Content {}

{... Some Code ...}

C:\PS>Assert-MockCalled Set-Content
````
This will throw an exception and cause the test to fail if Set-Content is not called in Some Code.

EXAMPLE 2
----------
````
C:\PS>Mock Set-Content -parameterFilter {$path.StartsWith("$env:temp\")}

{... Some Code ...}

C:\PS>Assert-MockCalled Set-Content 2 {$path=$env:temp\test.txt}
````
This will throw an exception if some code calls `Set-Content` on `$path=$env:temp\test.txt` less than 2 times 

EXAMPLE 3
-----------
````
C:\PS>Mock Set-Content {}

{... Some Code ...}

C:\PS>Assert-MockCalled Set-Content 0
````
This will throw an exception if some code calls Set-Content at all

EXAMPLE 4
-----------
````
C:\PS>Mock Set-Content {}

{... Some Code ...}

C:\PS>Assert-MockCalled Set-Content -Exactly 2
````
This will throw an exception if some code does not call Set-Content Exactly two times.

NOTES
-------
While Mock will only mock commands if the Parameter Filter
matches the Parameters passed to the command, Assert-MockCalled 
will count calls to a command whether they are mocked or not. 
The Command must be declared as a mock but the parameter 
filter in its mock declaration do not need to include the 
parameter Filter specified by Assert-MockCalled.