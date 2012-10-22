Provides syntactic sugar for logically grouping It blocks within a single Describe block.

PARAMETERS
-----------
###Name
The name of the Context. This is a phrase describing a set of tests within a describe.

###Fixture
Script that is executed. This may include setup specific to the context and one or more It blocks that validate the expected outcomes.

EXAMPLE
---------
````
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
````