Pester can output test results to a XML file using the NUnit 2.5 schema. 

[TeamCity](https://www.jetbrains.com/teamcity/)
-------------

TeamCity includes (since v4.5) a bundled XML Test Reporting plugin that can interpret the Pester Test Results file and include the test results in TeamCity's build dashboard. Configuring Pester and TeamCity to "light up" the Pester results is easy.

### Pester Settings

#### Using ./bin/pester.bat
If you are using the Pester .bat file to run your tests, test results are automatically output to Test.Xml in the root of the TeamCity agent working Directory.

### Using Invoke-Pester
If you are not using the Pester .bat file and are instead calling Invoke-Pester directly from a build script, Make sure to specify a path using the `-OutputFile` and type include the "-OutputFormat" parameters. The path is relative to the TeamCity agent working directory.

```powershell
Invoke-Pester -OutputFile Test.xml -OutputFormat NUnitXml
```

### TeamCity Settings

1. In your TeamCity build configuration settings, go to the "Build Step" page.
1. Add the "XML Report Processing" Build Feature.
1. The Report Type should be set to NUnit. You can select version 2.5.0.
1. In the Monitoring Rules text box, enter the xml file path given to Pester's `-OutputXml` parameter. If you use the Pester.bat file, simply enter Test.xml.

[AppVeyor](appveyor.com)
------------

The concept is the same: you serialize test results as `NUnitXml` and then upload them to the CI test reporting system.

Your [`appveyor.yml`](https://www.appveyor.com/docs/appveyor-yml) can contain section like this

```yaml
test_script:
    - ps: |
        $testResultsFile = ".\TestsResults.xml"
        $res = Invoke-Pester -OutputFormat NUnitXml -OutputFile $testResultsFile -PassThru
        (New-Object 'System.Net.WebClient').UploadFile("https://ci.appveyor.com/api/testresults/nunit/$($env:APPVEYOR_JOB_ID)", (Resolve-Path $testResultsFile))
        if ($res.FailedCount -gt 0) { 
            throw "$($res.FailedCount) tests failed."
        }
```

**Note**: `if` check with `throw` is used to fail the build, if any tests are failing.
**Note**: If tests write anything to the pipeline, then `$res` object will contain it as well.
Be careful!

**That's it! May all your tests be green!**