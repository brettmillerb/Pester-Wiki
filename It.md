Validates the results of a test inside of a `Describe` block.

DESCRIPTION
------------
The `It` command is intended to be used inside of a `Describe` or `Context` 
Block. If you are familiar with the AAA pattern 
(Arrange-Act-Assert), the body of the `It` block is the appropriate location 
for an assert. The convention is to assert a single 
expectation for each `It` block. The code inside of the `It` block 
should throw an exception if the expectation of the test is not 
met and thus cause the test to fail. The name of the `It` block 
should expressively state the expectation of the test.

In addition to using your own logic to test expectations and 
throw exceptions, you may also use Pester's `Should` command
to perform assertions in plain language.

PARAMETERS
-----------
###Name
An expressive phrase describing the expected test outcome.

###Test
The script block that should throw an exception if the 
expectation of the test is not met.  If you are following the 
AAA pattern (Arrange-Act-Assert), this typically holds the 
Assert. 

EXAMPLE
----------
```posh
function Add-Numbers($a, $b) {
    return $a + $b
}

Describe "Add-Numbers" {
    It "adds positive numbers" {
        $sum = Add-Numbers 2 3
        $sum | Should Be 5
    }

    It "adds negative numbers" {
        $sum = Add-Numbers (-2) (-2)
        $sum | Should Be (-4)
    }

    It "adds one negative number to positive number" {
        $sum = Add-Numbers (-2) 2
        $sum | Should Be 0
    }

    It "concatenates strings if given strings" {
        $sum = Add-Numbers two three
        $sum | Should Be "twothree"
    }

}
```