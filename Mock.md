Mocks the behavior of an existing command with an alternate 
implementation.

DESCRIPTION
--------------
This creates new behavior for any existing command within the scope of a 
Describe block. The function allows you to specify a ScriptBlock that will 
become the commands new behavior. 

Optionally you may create a Parameter Filter which will examine the 
parameters passed to the mocked command and will invoke the mocked 
behavior only if the values of the parameter values pass the filter. If 
they do not, the original command implementation will be invoked instead 
of the mock.

You may create multiple mocks for the same command, each using a different
ParameterFilter. ParameterFilters will be evaluated in reverse order of 
their creation. The last one created will be the first to be evaluated. 
The mock of the first filter to pass will be used. The exception to this 
rule are Mocks with no filters. They will always be evaluated last since 
they will act as a "catch all" mock.

Mocks can be marked Verifiable. If so, the Assert-VerifiableMocks can be 
used to check if all Verifiable mocks were actually called. If any 
verifiable mock is not called, Assert-VerifiableMocks will throw an 
exception and indicate all mocks not called.

The SUT (code being tested) that calls the actual commands that you have 
mocked must not be executing from inside a module. Otherwise, the mocked 
commands will not be invoked and the real commands will run. The SUT must 
be in the same Script scope as the test. So it must be either dot sourced, 
in the same file, or in a script file.

PARAMETERS
----------
###CommandName
The name of the command to be mocked.

###MockWith
A ScriptBlock specifying the behavior that will be used to mock CommandName.

###Verifiable
When this is set, the mock will be checked when using Assert-VerifiableMocks 
to ensure the mock was called.

###ParameterFilter
An optional filter to limit mocking behavior only to usages of 
CommandName where the values of the parameters passed to the command 
pass the filter.

This ScriptBlock must return a boolean value. See examples for usage.

EXAMPLE 1
-----------

    Mock Get-ChildItem {return @{FullName="A_File.TXT"}}

Using this Mock, all calls to `Get-ChildItem` will return an object with a 
`FullName` property returning "A_File.TXT"

EXAMPLE 2
-----------

    Mock Get-ChildItem {return @{FullName="A_File.TXT"}} -PrameterFilter {$Path.StartsWith($env:temp)}

This Mock will only be applied to `Get-ChildItem` calls within the user's temp directory.

EXAMPLE 3
----------

    Mock Set-Content {} -Verifiable -ParameterFilter {$Path -eq "some_path" -and $Value -eq "Expected Value"}

When this mock is used, if the Mock is never invoked and Assert-VerifiableMocks is called, an exception will be thrown. The command behavior will do nothing since the ScriptBlock is empty.

EXAMPLE 4
-----------
````
c:\PS>Mock Get-ChildItem {return @{FullName="A_File.TXT"}} -PrameterFilter {$Path.StartsWith($env:temp\1)}
c:\PS>Mock Get-ChildItem {return @{FullName="B_File.TXT"}} -PrameterFilter {$Path.StartsWith($env:temp\2)}
c:\PS>Mock Get-ChildItem {return @{FullName="C_File.TXT"}} -PrameterFilter {$Path.StartsWith($env:temp\3)}
````
Multiple mocks of the same command may be used. The parameter filter determines which is invoked. Here, if `Get-ChildItem` is called on the "2" directory of the temp folder, then B_File.txt will be returned.

EXAMPLE 5
-----------

    Mock Get-ChildItem {return @{FullName="B_File.TXT"}} -PrameterFilter {$Path -eq "$env:temp\me"}
    Mock Get-ChildItem {return @{FullName="A_File.TXT"}} -PrameterFilter {$Path.StartsWith($env:temp)}

    Get-ChildItem $env:temp\me

Here, both mocks could apply since both filters will pass. A_File.TXT will be returned because it was the last Mock created.

EXAMPLE 6
-----------
````
Mock Get-ChildItem {return @{FullName="B_File.TXT"}} -PrameterFilter {$Path -eq "$env:temp\me"}
Mock Get-ChildItem {return @{FullName="A_File.TXT"}}

Get-ChildItem c:\windows
````
Here, A_File.TXT will be returned. Since no filter was specified, it will apply to any call to `Get-ChildItem` that does not pass another filter.

EXAMPLE 7
-----------
````
Mock Get-ChildItem {return @{FullName="B_File.TXT"}} -PrameterFilter {$Path -eq "$env:temp\me"}
Mock Get-ChildItem {return @{FullName="A_File.TXT"}}

Get-ChildItem $env:temp\me
````
Here, B_File.TXT will be returned. Even though the filterless mock was created last. This illustrates that filterless Mocks are always evaluated last regardless of their creation order.
