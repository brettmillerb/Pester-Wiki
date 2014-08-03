Defines the context bounds of a test. One may use this block to 
encapsulate a scenario for testing - a set of conditions assumed
to be present and that should lead to various expected results
represented by the `It` blocks.

PARAMETERS
-------------
###Name
The name of the Test. This is often an expressive phrase describing the scenario being tested.

###Fixture
The actual test script. If you are following the AAA pattern (Arrange-Act-Assert), this 
typically holds the arrange and act sections. The Asserts will also lie in this block but are 
typically nested each in its own `It` block.  Assertions are typically performed by the `Should`
command within the `It` blocks.

###Tags
Optional parameter containing an array of strings.  When calling `Invoke-Pester`, it is possible to specify a `-Tag` parameter which will only execute `Describe` blocks containing the same Tag.

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