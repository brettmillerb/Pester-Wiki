Provides logical grouping of `It` blocks within a single `Describe` block.

Any `Mock`s defined inside a `Context` are removed at the end of the `Context` scope, as are any files or folders added to the 'TestDrive:\' path during the `Context` block's execution.

Any `BeforeEach` or `AfterEach` blocks defined inside a `Context` also only apply to tests within that `Context` scope.

PARAMETERS
-----------
### Name
The name of the Context. This is a phrase describing a set of tests within a describe.

### Tag
Optional parameter containing an array of strings. When calling `Invoke-Pester`,
it is possible to specify a `-Tag` parameter which will only execute Context blocks
containing the same Tag.

### Fixture
Script that is executed. This may include setup specific to the context and one or more It blocks that validate the expected outcomes.

EXAMPLE
---------
```posh
Describe "Description Name" {
    Context "Context Name #1" {
         It "..." { ... }
    }

    Context "Context Name #2" {
        It "..." { ... }
        It "..." { ... }
        It "..." { ... }
    }
}
```
EXAMPLE
---------
```posh
function Add-Numbers($a, $b) {
    return $a + $b
}

Describe "Add-Numbers" {

    Context "when root does not exist" {
         It "..." { ... }
    }

    Context "when root does exist" {
        It "..." { ... }
        It "..." { ... }
        It "..." { ... }
    }
}
```