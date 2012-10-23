Pester can output test results to a XML file using the NUnit 2.5 schema. TeamCity includes (since v4.5) a bundled XML Test Reporting plugin that can interpret the Pester Test Results file and include the test results in TeamCity's build dashboard. Configuring Pester and TeamCity to "light up" the Pester results is easy.

Pester Settings
----------------
###Using ./bin/pester.bat
If you are using the Pester .bat file to run your tests, test results are automatically output to Test.Xml in the root of the TeamCity agent working Directory.

###Using Invoke-Pester
If you are not using the Pester .bat file and are instead calling Invoke-Pester directly from a build script, Make sure to specify a path using the `-OutputXml` parameter. The path is relative to the TeamCity agent working directory.

    Invoke-Pester -OutputXml Test.xml

TeamCity Settings
------------------
1. In your TeamCity build configuration settings, go to the "Build Step" page.
1. Add the "XML Report Processing" Build Feature.
1. The Report Type should be set to NUnit. You can select version 2.5.0.
1. In the Monitoring Rules text box, enter the xml file path given to Pester's `-OutputXml` parameter. If you use the Pester.bat file, simply enter Test.xml.

####That's it! May all your tests be green!