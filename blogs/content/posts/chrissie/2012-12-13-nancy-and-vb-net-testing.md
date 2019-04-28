---
title: 'Nancy and VB.Net: testing your modules.'
author: Christiaan Baes (chrissie1)
type: post
date: 2012-12-13T10:40:00+00:00
ID: 1853
excerpt: Testing our modules in Nancy.
url: /index.php/webdev/serverprogramming/nancy-and-vb-net-testing/
views:
  - 4733
categories:
  - ASP.NET
  - Server Programming
tags:
  - nancy
  - vb.net

---
## Introduction

In [my previous post][1] I made a Plantsmodule which had 2 routes. One of those routes had a problem when tried to put a string in the parameter and that was less than optimal. It was time for tests to make it easier on myself to test these kinds of things.

## The module

So here is my module.

```vbnet
Imports WebApplication2.Model
Imports Nancy

Public Class PlantsModule
    Inherits NancyModule

    Public Sub New()
        MyBase.Get("/plants") = Function(parameters)
                                    Return View(New PlantsModel() With {.Plants = New List(Of PlantModel) From {New PlantModel() With {.Id = 2, .Name = "test"}}})

                                End Function
        MyBase.Get("/plants/{Id}") = Function(parameters)
                                         If parameters.id = 1 Then
                                             Return View(New PlantModel() With {.Id = 1, .Name = "test"})
                                         Else
                                             Return View(New PlantModel() With {.Id = 2, .Name = "test"})
                                         End If
                                     End Function

    End Sub

End Class```
This one throws an exception when you enter abc or any other bad value.

## The tests

To begin with you should nuget the Nancy.Testing package.

Then you can use the Browser.

For our purpose it would look something like this.

```vbnet
Private _browser As Browser

    &lt;SetUp()&gt;
    Public Sub FixtureSetup()
        Dim configuration = A.Fake(Of IRazorConfiguration)()
        Dim bootstrapper = New ConfigurableBootstrapper(Sub(config)
                                                            config.Module(Of PlantsModule)()
                                                            config.ViewEngine(New RazorViewEngine(configuration))
                                                        End Sub)
        _browser = New Browser(bootstrapper)
    End Sub```
So we tell it what Module to use and we tell it what Engine to use. We also use FakeItEasy to make a mock for our RazorConfiguration. For the next part I got some help from [this blogpost by Jef Claes][2].

if you leave out the Engine you will get this error.

> System.Exception : ConfigurableBootstrapper Exception
    
> &#8212;-> Nancy.RequestExecutionException : Oh noes!
    
> &#8212;-> Nancy.ViewEngines.ViewNotFoundException : Unable to locate view &#8216;Plants&#8217;
  
> Currently available view engine extensions: sshtml,html,htm
  
> Locations inspected: ,,,,views/Plants/Plants,Plants/Plants,views/Plants,Plants
  
> Root path: E:UserschristiaanAppDataLocalNCrunch708461WebApplication2bin 

Do you notice how it does not mention cshtml or vbhtml in view engine extensions? That is solved by telling it what engine to use.

In theory that should work but you will get this error when you try it.

> System.Exception : ConfigurableBootstrapper Exception
    
> &#8212;-> Nancy.RequestExecutionException : Oh noes!
    
> &#8212;-> Nancy.ViewEngines.ViewNotFoundException : Unable to locate view &#8216;Plants&#8217;
  
> Currently available view engine extensions: sshtml,html,htm,cshtml,vbhtml
  
> Locations inspected: ,,,,views/Plants/Plants,Plants/Plants,views/Plants,Plants
  
> Root path: E:UserschristiaanAppDataLocalNCrunch708461WebApplication2bin 

That is solved by the RootPathProvider we find in the blogpost by Jef Claes. In VB.Net that would look like this.

```vbnet
Imports System.IO
Imports Nancy

Public Class RootPathProvider
    Implements IRootPathProvider

    Private Shared _cachedRootPath As String

    Public Function GetRootPath() As String Implements IRootPathProvider.GetRootPath
        If Not String.IsNullOrEmpty(_cachedRootPath) Then Return _cachedRootPath
        Dim currentDirectory = New DirectoryInfo(Environment.CurrentDirectory)
        Dim rootPathFound = False
        While Not rootPathFound
            Dim directoriesContainingViewFolder = currentDirectory.GetDirectories("Views", SearchOption.AllDirectories)
            If directoriesContainingViewFolder.Any() Then
                _cachedRootPath = directoriesContainingViewFolder.First().FullName
                rootPathFound = True
            End If
            currentDirectory = currentDirectory.Parent
        End While
        Return _cachedRootPath
    End Function

    Public Function Equals1(obj As Object) As Boolean Implements IHideObjectMembers.Equals

    End Function

    Public Function GetHashCode1() As Integer Implements IHideObjectMembers.GetHashCode

    End Function

    Public Function GetType1() As Type Implements IHideObjectMembers.GetType

    End Function

    Public Function ToString1() As String Implements IHideObjectMembers.ToString

    End Function
End Class```
The above presumes your views are in a Views subfolder. Which they are of course.
  
And now we can start writing tests.

```vbnet
&lt;Test&gt;
    Public Sub IfPlantsRouteReturnsStatusCodeOk()
        Dim result = _browser.Get("/plants", Sub(x)
                                                 x.HttpRequest()
                                             End Sub)
        Assert.AreEqual(HttpStatusCode.OK, result.StatusCode)
    End Sub

    &lt;Test&gt;
    Public Sub IfPlantWithId1RouteReturnsStatusCodeOk()
        Dim result = _browser.Get("/plants/1", Sub(x)
                                                   x.HttpRequest()
                                               End Sub)
        Assert.AreEqual(HttpStatusCode.OK, result.StatusCode)
    End Sub

    &lt;Test&gt;
    Public Sub IfPlantWithIdabcRouteReturnsStatusCodeOk()
        Dim result = _browser.Get("/plants/abc", Sub(x)
                                                     x.HttpRequest()
                                                 End Sub)
        Assert.AreEqual(HttpStatusCode.NotFound, result.StatusCode)
    End Sub```
I just want to check if I get an ok code when it works and a not found when I do plants/abc

But the last test fails. With this exception.

```vbnet
System.Exception : ConfigurableBootstrapper Exception
  ----&gt; Nancy.RequestExecutionException : Oh noes!
  ----&gt; System.InvalidCastException : Conversion from string "abc" to type 'Double' is not valid.
  ----&gt; System.FormatException : Input string was not in a correct format.
```
Time to change our Module. And do the simplest thing to make our test pass.

```vbnet
Imports WebApplication2.Model
Imports Nancy

Public Class PlantsModule
    Inherits NancyModule

    Public Sub New()
        MyBase.Get("/plants") = Function(parameters)
                                    Return View(New PlantsModel() With {.Plants = New List(Of PlantModel) From {New PlantModel() With {.Id = 2, .Name = "test"}}})

                                End Function
        MyBase.Get("/plants/{Id}") = Function(parameters)
                                         If Not IsNumeric(parameters.id) Then Return HttpStatusCode.NotFound
                                         If parameters.id = 1 Then
                                             Return View(New PlantModel() With {.Id = 1, .Name = "test"})
                                         Else
                                             Return View(New PlantModel() With {.Id = 2, .Name = "test"})
                                         End If
                                     End Function

    End Sub

End Class```
And now all three of our tests pass.

Don&#8217;t worry I can write more tests.

## Conclusion

I figured it out, so it isn&#8217;t that hard ;-).

<span class="MT_red">Update</span>

Since version 0.15 the viewengine also needs a textresource. So now the setup looks like this.

```vbnet
&lt;SetUp()&gt;
        Public Sub FixtureSetup()
            Dim configuration = A.Fake(Of IRazorConfiguration)()
            Dim textResource = A.Fake(Of ITextResource)()
            Dim bootstrapper = New ConfigurableBootstrapper(Sub(config)
                                                                config.Module(Of PlantsModule)()
                                                                config.Dependency(Of ITextResource)(textResource)
                                                                config.ViewEngine(New RazorViewEngine(configuration, textResource))
                                                            End Sub)
            _browser = New Browser(bootstrapper)
        End Sub```

 [1]: /index.php/WebDev/ServerProgramming/nancy-and-vb-net-the
 [2]: http://www.jefclaes.be/2012/06/making-my-first-nancyfx-test-pass.html