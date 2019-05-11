---
title: Running Nunit tests from your code
author: Christiaan Baes (chrissie1)
type: post
date: 2016-11-09T11:21:09+00:00
ID: 4805
excerpt: "How to run tests in your VB.Net script, and if you ask nicely I'm sure this works in C# too. "
url: /index.php/uncategorized/running-nunit-tests-from-your-code/
views:
  - 2189
rp4wp_auto_linked:
  - 1
categories:
  - Uncategorized

---
So I had this script that makes our dev and test environment and blahblah. In other words I needed to verify that what I had was good before starting the process and that checked if everything succeeded as it was supposed to after the script runs. Seems like what I needed were a bunch of tests. First to check no one tampered with the bits I needed to make this work and in the end see if the things I did worked. I repeated myself didn't I? Good.

So first of all I wrote some simple tests and found this piece of code on the [StackOverflow site][1] 

```vbnet
Namespace TestFramework

    Public Class AssertAll

        Public Shared Function Succeed(ParamArray assertions As Action()) As String
            Dim errors = New List(Of String)()
            Console.WriteLine("Running {0} tests.", assertions.Count)
            For Each assertion In assertions
                Try
                    assertion()
                Catch ex As Exception
                    errors.Add(String.Format("Test {0} failed with message {1}", assertion.Method.Name, ex.Message))
                End Try
            Next
            If (errors.Any()) Then
                Console.WriteLine("{0} tests failed, {1} tests passed.", errors.Count, assertions.Count - errors.Count)
                For Each errorDescription In errors
                    LogTo.Warn(errorDescription)
                Next
                Throw New Exception(String.Format("{0} tests failed, {1} tests passed.", errors.Count, assertions.Count - errors.Count))
            End If
            Console.WriteLine("All {0} tests passed.", assertions.Count)
            Return String.Format("All {0} tests passed.", assertions.Count)
        End Function

    End Class
End Namespace
```

This ofcourse wants you to pass it some actions which are our tests.

```vbnet
Public Function Execute() As String
            Console.WriteLine("Running tests for {0}", Me.GetType.Name)
            Dim methods = Me.GetType().GetMethods().Where(Function(m) m.GetCustomAttributes(GetType(TestAttribute), False).Length > 0)
            Dim tests = (From t In methods Select [Delegate].CreateDelegate(GetType(Action), Me, t)).Cast(Of Action)().ToList()
            Return AssertAll.Succeed(tests.ToArray)
        End Function
```

Or something like that.
  
This was in a baseclass from which testclasses inherit (so you can pass some much needed parameters to them and then you just call execute and BAM! tests run. 

This however only works for "simple" tests. As soon as you start to use testcase you are in trouble and a world of much code. Much code means much work and I don't like to work. But I don't have too. Because I can run the tests another way, with nunit.engine. 

Just nuget the NUnit.Engine package and make sure everyone of those files is in your outputfolder

[<img src="/wp-content/uploads/2016/11/nunitengine.png" alt="nunitengine" width="184" height="184" class="alignnone size-full wp-image-4806" />][2]

(I don't care how you do this just make it happen, no need to kill kittens for this, but if you must). 

I guess you don't need the config or addins file. 

After that you can run tests with this.

```vbnet
Imports System.Reflection
Imports System.Xml
Imports NUnit.Engine

Namespace TestFramework

    Public Class RunTests

        Public Shared Function Run(ByVal category As String) As String
            Dim returnvalue = ""
            Dim path = Assembly.GetExecutingAssembly().Location
            Dim package = New TestPackage(path)
            package.AddSetting("WorkDirectory", Environment.CurrentDirectory)

            Dim engine = TestEngineActivator.CreateInstance()
            Dim filterService = engine.Services.GetService(Of ITestFilterService)()
            Dim builder = filterService.GetTestFilterBuilder()
            builder.SelectWhere("cat==" & category)
            Dim emptyFilter = builder.GetFilter

            Using runner = engine.GetRunner(package)
                Dim explore = runner.CountTestCases(emptyFilter)
                Console.WriteLine("Running {0} tests.", explore)
                Dim result = runner.Run(New Listener, emptyFilter)
                If result.Attributes.GetNamedItem("result").InnerText = "Passed" Then
                    returnvalue = String.Format("All {0} tests passed.", result.Attributes.GetNamedItem("total").InnerText)
                    Console.WriteLine("All {0} tests passed.", result.Attributes.GetNamedItem("total").InnerText)
                Else
                    Console.WriteLine("Some of the {0} tests failed. Passed: {1} , failed {2}", result.Attributes.GetNamedItem("total").InnerText, result.Attributes.GetNamedItem("passed").InnerText, result.Attributes.GetNamedItem("failed").InnerText)
                    For Each r As XmlNode In result.SelectNodes("//test-case[contains(@result, 'Failed')]")
                        If r.SelectSingleNode("//failure").ChildNodes.Count = 1 Then
                            Console.WriteLine("Test {0} failed with error {1}.", r.Attributes.GetNamedItem("name").InnerText, r.SelectSingleNode("//failure").ChildNodes(0).InnerText)
                        Else
                            Console.WriteLine("Test {0} failed with error {1} with stacktrace {2}.", r.Attributes.GetNamedItem("name").InnerText, r.SelectSingleNode("//failure").ChildNodes(0).InnerText, r.SelectSingleNode("//failure").ChildNodes(1).InnerText)
                        End If
                    Next
                    Throw New Exception(String.Format("Some of the {0} tests failed. Passed: {1} , failed {2}", result.Attributes.GetNamedItem("total").InnerText, result.Attributes.GetNamedItem("passed").InnerText, result.Attributes.GetNamedItem("failed").InnerText))
                End If
            End Using
            Return returnvalue
        End Function
    End Class


    Public Class Listener
        Implements ITestEventListener

        Public Sub OnTestEvent(report As String) Implements ITestEventListener.OnTestEvent
            Console.WriteLine(report)
        End Sub

    End Class

End Namespace
```
As you can see I have a filter on category for the above.

This might be of use for you

  * [NUnit.Engine.Api][3]
  * [The tests to figure out what goes in the SelectWhere method.][4]
  * [The testresults xml format][5]
  * [One of the errors you might get and a solution][6]

And that's it. Any questions?

 [1]: http://stackoverflow.com/questions/2834717/nunit-is-it-possible-to-continue-executing-test-after-assert-fails
 [2]: /wp-content/uploads/2016/11/nunitengine.png
 [3]: https://github.com/nunit/docs/wiki/Test-Engine-API-Spec
 [4]: https://github.com/nunit/nunit-console/blob/8cb7226c0ad683fe5eb7a4eee8989aff08dc4ccb/src/NUnitEngine/nunit.engine.tests/Services/TestFilterBuilderTests.cs
 [5]: https://github.com/nunit/docs/wiki/Test-Result-XML-Format
 [6]: http://stackoverflow.com/questions/12092117/nunit-components-for-version-4-0-30319-of-the-clr-are-not-installed