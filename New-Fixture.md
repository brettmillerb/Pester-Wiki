Generates scaffolding for two files: One that defines a function and another one that contains its tests.

DESCRIPTION
------------
This command generates two files located in a directory 
specified in the path parameter. If this directory does 
not exist, it will be created. A file containing a function 
signature named after the name parameter(ie name.ps1) and 
a test file containing a scaffolded describe and it blocks.

EXAMPLE
----------

    New-Fixture deploy Clean

Creates two files:
````
./deploy/Clean.ps1
function clean {

    }

./deploy/clean.Tests.ps1
$here = Split-Path -Parent $MyInvocation.MyCommand.Path
    $sut = (Split-Path -Leaf $MyInvocation.MyCommand.Path).Replace(".Tests.", ".")
    . "$here\$sut"

    Describe "clean" {

        It "does something useful" {
            $true.should.be($false)
        }
    }
````