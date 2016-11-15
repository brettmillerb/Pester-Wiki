New-MockObject is a Pester function (introduced in Pester 4.0.0) that allows you to create "fake" objects of almost any type to run in Pester mocks. These "fake objects" allow your mocks to return the same type as the function it was mocking to pass the result to entities that are strongly typed.

To explain the problem that `New-MockObject` solves, let's dive into the problem itself and show how this scenario cannot be solved without it. To demonstrate this, I'm working with a script and two functions inside of that script.

Do-Thing.ps1
================
    function Set-Thing {
        param (
            [Thing.Type]$Thing ## $Thing is STRONGLY typed. This param MUST be Thing.Type
        )

        ## Stuff that does something to thing here
    }

    function Get-Thing {
        param (
            [string]$ThingLabel
        )

        [Thing.Type]::Get($ThingLabel) ## Must return an object of Thing.Type
    }

    $thing = Get-Thing -ThingLabel 'whatever'
    Set-Thing -Thing $thing ## Requires $thing to be of type Thing.Type
======================

This script retrieves a thing based on a string `ThingLabel` parameter using `Get-Thing` and then uses the output of that command to use as a parameter Thing in the `Set-Thing` command.

Now, I want to create some Pester tests for this scenario and ensure it executes correctly. An example Pester test might look something like this:

    describe 'Set the thing' {
    
        mock 'Get-Thing' {
            [pscustomobject]@{ Property = 'Value' }
        }

        mock 'Set-Thing'

        .\Do-Thing.ps1    

        it 'sets the right thing' {
            $assMParams = @{
                'CommandName' = 'Set-Thing'
                'Times' = 1
                'Exactly' = $true
                'ParameterFilter' = {$Thing -eq '????' }
            }
            Assert-MockCalled @assMParams 
        }
    }

Your assertion will never even get called because `Set-Thing`'s `Thing` parameter must be of type `Thing.Type`. This means that `Get-Thing` must output that type. As-is, my mock of `Get-Thing` is just returning an object of `[System.Management.Automation.PSCustomObject]`. Since the `Thing` parameter on `Set-Thing` is strongly typed, this will never work and will fail at this step.

The solution is to change the `Get-Thing` mock to return an object with the type of `Thing.Type`. But, this is sometimes easier said than done. We would typically do this with the `New-Object` command, but this relies on the classes having public constructors. Even if the class does have public constructors, the arguments themselves might be objects themselves that either have no public constructors or might require narrowing down how to instantiate one.

Even if the object you initially set out to mock and all of the arguments have public constructors and you manage to create this object, not all objects have public constructors. Some only have private constructors that are not even possible to create with `New-Object`! There's got to be a better way. Lucky for us, there is now with `New-MockObject`.

`New-MockObject` does not rely on constructors. It instead creates "fake" objects that look just like the original that use constructors. Once created, these "fake" objects can be passed to anything that requires a particular object type, and it will never know the difference. This means that it's now possible to create Pester tests for this scenario using `New-MockObject`.

The only part that would need to be changed is the mock to `Get-Thing`. Now, depending on if the required .NET assembly is available; you can simply mock `Get-Thing` to output whatever type of object you want regardless of the circumstances.

    mock 'Get-Thing' {
        New-MockObject -Type Thing.Type
    }

At this point, the test would run as you would expect!