---
title: 'Nancy and localization: Testing'
author: Christiaan Baes (chrissie1)
type: post
date: 2015-03-06T09:55:19+00:00
ID: 3288
url: /index.php/uncategorized/nancy-and-localization-testing/
views:
  - 1341
categories:
  - Uncategorized

---
In my previous posts about this subject [Nancy and localization: how I think it works][1], [Nancy and localization: The cookie approach][2] and [Nancy and localization: The better cookie approach][3] I explored how localization works in Nancy. I however forgot to write tests to see if my code actually works.

So I continue on adding to that project but for your comfort I will repeat the code here.

So here is now the current module.

```vbnet
Imports System.Globalization
Imports Nancy

Namespace Modules
    Public Class HomeModule
        Inherits NancyModule

        Public Sub New()
            [Get]("/") = Function(parameters)
                             Return View("Home")
                         End Function
            [Get]("/{locale}") = Function(parameters)
                                     Try
                                         Context.Culture = New CultureInfo(parameters.locale.ToString)
                                     Catch ex As Exception
                                         Context.Culture = New CultureInfo("en-US")
                                     End Try
                                     Return View("Home").WithCookie(New Cookies.NancyCookie("CurrentCulture", Context.Culture.Name))
                                 End Function
        End Sub

    End Class
End Namespace
```
Here is my ever so slightly altered view.

```html
@Code
    Layout = Nothing
End Code```<div>
  <a href="\nl-BE">Nederlands</a><br /> <a href="\en-US">English</a></p> 
  
  <p id="myId">
    @Text.Home.MyString
  </p>
  
  <p>
    &nbsp;
  </p>
</div>

&nbsp;

I added an id to the p tag so I can more easily test it.

And then I have two resx files in my Resources folder. One called Home.resx and the other Home.nl-BE.resx.

And now I added a second project that contains my tests. VERY IMPORTANT! It works slightly differently when in the same project.

So I create the most minimal of Tests using NUnit and Fakeiteasy.

```vbnet
Imports FakeItEasy
Imports Nancy.Testing
Imports Nancy.ViewEngines.Razor
Imports NUnit.Framework

Namespace Modules
    Public Class TestHomeModule
        Private _browser As Browser

        &lt;SetUp()&gt;
        Public Sub FixtureSetup()
            Dim configuration = A.Fake(Of IRazorConfiguration)()
            Dim bootstrapper = New ConfigurableBootstrapper(Sub(config)
                                                                config.Module(Of WebApplication3.Modules.HomeModule)()
                                                                config.RootPathProvider(Of RootPathProvider)()
                                                                config.ViewEngine(New RazorViewEngine(configuration))
                                                            End Sub)
            _browser = New Browser(bootstrapper)
        End Sub

        
        Public Sub IfGetWithCultureEnUsReturnsViewOk()
            Dim result = _browser.Get("/", Sub(x)
                                               x.HttpRequest()
                                               x.Cookie("CurrentCulture", "en-US")
                                           End Sub)
            result.Body("#myId").ShouldExistOnce().And.ShouldContain("English")
        End Sub

        
        Public Sub IfGetWithCultureNlBeReturnsViewOk()
            Dim result = _browser.Get("/", Sub(x)
                                               x.HttpRequest()
                                               x.Cookie("CurrentCulture", "nl-BE")
                                           End Sub)
            result.Body("#myId").ShouldExistOnce().And.ShouldContain("Nederlands")
        End Sub

    End Class
End Namespace
```
And those tests fail. Bad tests.

Judging from the error the resource files were not found. So we have to tell Nancy where to find our resources.

We do this by adding this to the FixtureSetup.

```vbnet
Dim resourceAssemblyProvider = A.Fake(Of IResourceAssemblyProvider)()
A.CallTo(Function() resourceAssemblyProvider.GetAssembliesToScan()).Returns(New List(Of Assembly) From {GetType(WebApplication3.Modules.HomeModule).Assembly})
```
And now the tests pass. Probably because Nancy does some scanning and finds an IResourceAssemblyProvider in my code and uses that. Because in no way do I tell anyone where to use it. So magic.
  
So for completeliness the whole test class.

```vbnet
Imports System.Reflection
Imports FakeItEasy
Imports Nancy
Imports Nancy.Testing
Imports Nancy.ViewEngines.Razor
Imports NUnit.Framework

Namespace Modules
    Public Class TestHomeModule
        Private _browser As Browser

        &lt;SetUp()&gt;
        Public Sub FixtureSetup()
            Dim configuration = A.Fake(Of IRazorConfiguration)()
            Dim resourceAssemblyProvider = A.Fake(Of IResourceAssemblyProvider)()
            A.CallTo(Function() resourceAssemblyProvider.GetAssembliesToScan()).Returns(New List(Of Assembly) From {GetType(WebApplication3.Modules.HomeModule).Assembly})
            Dim bootstrapper = New ConfigurableBootstrapper(Sub(config)
                                                                config.Module(Of WebApplication3.Modules.HomeModule)()
                                                                config.RootPathProvider(Of RootPathProvider)()
                                                                config.ViewEngine(New RazorViewEngine(configuration))
                                                            End Sub)
            _browser = New Browser(bootstrapper)
        End Sub

        
        Public Sub IfGetPimsCase200102283WithCultureEnUsReturnsViewOk()
            Dim result = _browser.Get("/", Sub(x)
                                               x.HttpRequest()
                                               x.Cookie("CurrentCulture", "en-US")
                                           End Sub)
            result.Body("#myId").ShouldExistOnce().And.ShouldContain("English")
        End Sub

        
        Public Sub IfGetPimsCase200102283WithCultureNlBeReturnsViewOk()
            Dim result = _browser.Get("/", Sub(x)
                                               x.HttpRequest()
                                               x.Cookie("CurrentCulture", "nl-BE")
                                           End Sub)
            result.Body("#myId").ShouldExistOnce().And.ShouldContain("Nederlands")
        End Sub

    End Class
End Namespace
```
And now I can test if my resources are filled in correctly in the correct place.

<span style="color: #ff0000;">Edit</span>

The above didn&#8217;t seem very stable with ncrunch. Sometimes the tests passed sometimes they didn&#8217;t. So I added this.

```vbnet
Dim textResource = New ResourceBasedTextResource(resourceAssemblyProvider)
```
And this to the ConfigurableBootstrapper.

```vbnet
config.TextResource(textResource)
```
So the whole testclass now looks like this.

```vbnet
Imports System.Reflection
Imports FakeItEasy
Imports Nancy
Imports Nancy.Localization
Imports Nancy.Testing
Imports Nancy.ViewEngines.Razor
Imports NUnit.Framework

Namespace Modules
    Public Class TestHomeModule
        Private _browser As Browser

        &lt;SetUp()&gt;
        Public Sub FixtureSetup()
            Dim configuration = A.Fake(Of IRazorConfiguration)()
            Dim resourceAssemblyProvider = A.Fake(Of IResourceAssemblyProvider)()
            A.CallTo(Function() resourceAssemblyProvider.GetAssembliesToScan()).Returns(New List(Of Assembly) From {GetType(WebApplication3.Modules.HomeModule).Assembly})
            Dim textResource = New ResourceBasedTextResource(resourceAssemblyProvider)
            Dim bootstrapper = New ConfigurableBootstrapper(Sub(config)
                                                                config.Module(Of WebApplication3.Modules.HomeModule)()
                                                                config.TextResource(textResource)
                                                                config.RootPathProvider(Of RootPathProvider)()
                                                                config.ViewEngine(New RazorViewEngine(configuration))
                                                            End Sub)
            _browser = New Browser(bootstrapper)
        End Sub

        
        Public Sub IfGetPimsCase200102283WithCultureEnUsReturnsViewOk()
            Dim result = _browser.Get("/", Sub(x)
                                               x.HttpRequest()
                                               x.Cookie("CurrentCulture", "en-US")
                                           End Sub)
            result.Body("#myId").ShouldExistOnce().And.ShouldContain("English")
        End Sub

        
        Public Sub IfGetPimsCase200102283WithCultureNlBeReturnsViewOk()
            Dim result = _browser.Get("/", Sub(x)
                                               x.HttpRequest()
                                               x.Cookie("CurrentCulture", "nl-BE")
                                           End Sub)
            result.Body("#myId").ShouldExistOnce().And.ShouldContain("Nederlands")
        End Sub

    End Class
End Namespace
```

 [1]: /index.php/uncategorized/nancy-and-localization-how-i-think-it-works/
 [2]: /index.php/uncategorized/nancy-and-localization-the-cookie-approach/
 [3]: /index.php/uncategorized/nancy-and-localization-the-better-cookie-approach/