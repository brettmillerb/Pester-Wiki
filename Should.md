`Should` is a command that provides assertion convenience methods for comparing objects and throwing test failures when test expectations fail.  `Should` should be used inside `It` blocks of a Pester test script.

SHOULD OPERATORS
--------------
###Be
Compares one object with another for equality and throws if the two objects are not the same.  This comparison is not case sensitive.

```posh
$actual="Actual value"
$actual | Should Be "actual value" #Nothing happens
$actual | Should Be "not actual value"  #A Pester Failure is thrown
```

###BeExactly
Compares one object with another for equality and throws if the two objects are not the same.  This comparison is case sensitive.

```posh
$actual="Actual value"
$actual | Should BeExactly "Actual value" #Nothing happens
$actual | Should BeExactly "actual value" #A Pester Failure is thrown
```

###Exist
Does not perform any comparison but checks if the object calling Exist is present in a PS Provider. The object must have valid path syntax. It essentially must pass a Test-Path call.

```posh
$actual=(Dir . )[0].FullName
Remove-Item $actual
$actual | Should Exist #Will fail
```

###Contain
Checks to see if a file contains the specified text.  This search is not case sensitive.

```posh
Set-Content -Path TestDrive:\file.txt -Value 'I am a file.'
'TestDrive:\file.txt' | Should Contain 'I Am' # Test will pass
'TestDrive:\file.txt' | Should Contain 'I Am Not' # Test will fail
```

###ContainExactly
Checks to see if a file contains the specified text.  This search is case sensitive.

```posh
Set-Content -Path TestDrive:\file.txt -Value 'I am a file.'
'TestDrive:\file.txt' | Should Contain 'I am' # Test will pass
'TestDrive:\file.txt' | Should Contain 'I Am' # Test will fail
```

###Match
Uses a regular expression to compare two objects.  This comparison is not case sensitive.

```posh
"I am a value" | Should Match "I Am" #Passes
"I am a value" | Should Match "I am a bad person" #will fail
```

###MatchExactly
Uses a regular expression to compare two objects.  This comparison is case sensitive.

```posh
"I am a value" | Should MatchExactly "I am" #Passes
"I am a value" | Should MatchExactly "I Am" #will fail
```

###Throw
Checks if an exception was thrown. The object must be a ScriptBlock.

```posh
{ foo } | Should Throw #Passes
{ $foo = 1 } | Should Throw #Will fail
{ foo } | Should Not Throw #Will fail
{ $foo = 1 } | Should Not Throw #Passes
```

###BeNullOrEmpty
Checks values for null or empty (strings). Compare with C# string.IsNullOrEmpty method.

```posh
$null | Should BeNullOrEmpty #success
$null | Should Not BeNullOrEmpty #fails
@()   | Should BeNullOrEmpty #success
""    | Should BeNullOrEmpty #success
```

USING SHOULD IN A TEST
----------------------

```posh
function Add-Numbers($a, $b) {
	return $a + $b
}

Describe "Add-Numbers" {

	It "adds positive numbers" {
	    $sum = Add-Numbers 2 3
	    $sum | should be 3
	}
            
    It "ensures that that 2 + 2 does not equal 5" {
	    $sum = Add-Numbers 2 2
	    $sum | should not be 5
	}
}
```

This test will fail since 3 will not be equal to the sum of 2 and 3.