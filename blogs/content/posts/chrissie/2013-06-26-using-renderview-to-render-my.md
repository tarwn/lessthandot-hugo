---
title: Using RenderView to render my custom errorpages in Nancy
author: Christiaan Baes (chrissie1)
type: post
date: 2013-06-26T12:26:00+00:00
ID: 2121
excerpt: A better way to make your custom error pages with Nancy.
url: /index.php/webdev/serverprogramming/using-renderview-to-render-my/
views:
  - 5847
categories:
  - ASP.NET
  - Server Programming
tags:
  - custom errors
  - nancy

---
## Introduction

I already wrote about making [custom errorpages in Nancy a few months ago][1]. And I used some static htm pages for those and a stream to get them to show on your screen. 

This was all fine and dandy but I&#8217;m sure I can do better.

## Using RenderView

First of all I can make use of the Nancy View Rendereing and make my errorpages RazorViews like the rest of them. 

So I moved 404.htm and 500.htm to the Views folder in a subfolder errorpages.

And then changed the CustomErros.vb file to this.

```vbnet
Imports Nancy
Imports Nancy.ErrorHandling
Imports Nancy.ViewEngines

Namespace ErrorPages

    Public Class CustomErrors
        Inherits DefaultViewRenderer
        Implements IStatusCodeHandler

        Private ReadOnly _errorPages As IDictionary(Of HttpStatusCode, String)
        Private ReadOnly _supportedStatusCodes As HttpStatusCode() = {HttpStatusCode.NotFound, HttpStatusCode.InternalServerError}

        Public Sub New(factory As IViewFactory)
            MyBase.New(factory)
            _errorPages = New Dictionary(Of HttpStatusCode, String) From
                {
                {HttpStatusCode.NotFound, "errorpages/404"},
                {HttpStatusCode.InternalServerError, "errorpages/500"}
                }
        End Sub

        Public Sub Handle(statusCode As HttpStatusCode, context As NancyContext) Implements IStatusCodeHandler.Handle
            Dim errorPage As String
            If Not _errorPages.TryGetValue(statusCode, errorPage) Then
                Return
            End If
            If String.IsNullOrEmpty(errorPage) Then
                errorPage = "500"
            End If

            Dim response = RenderView(context, errorPage)
            response.StatusCode = statusCode
            context.Response = response
        End Sub

        Public Function HandlesStatusCode(statusCode As HttpStatusCode, context As NancyContext) As Boolean Implements IStatusCodeHandler.HandlesStatusCode
            Return _supportedStatusCodes.Any(Function(s) s = statusCode)
        End Function

    End Class
End Namespace```
And that&#8217;s all the code you need.

The above will return a 404 if needed and a 500 on any other exception. YOu could probably make that better.

Now there is a problem with the above. It will always return a htm page even if the people on the other side request json.

I found that code [here][2] BTW. 

## Making the response return json if needed

I did the above because I [read this by Paul Stovell][3]

So first thing to do was to create my ErrorResponse Clas, like Paul does.

```vbnet
Imports Nancy
Imports Nancy.Responses

Namespace ErrorPages
    Public Class ErrorResponse
        Inherits JsonResponse

        Private ReadOnly _errorClass As ErrorClass

        Private Sub New(errorClass As ErrorClass)
            MyBase.New(errorClass, New DefaultJsonSerializer)
            _errorClass = errorClass
        End Sub

        Public ReadOnly Property ErrorMessage As String
            Get
                Return _errorClass.ErrorMessage
            End Get
        End Property

        Public ReadOnly Property FullException As String
            Get
                Return _errorClass.FullException
            End Get
        End Property

        Public Shared Function FromMessage(message As String) As ErrorResponse
            Dim response = New ErrorResponse(New ErrorClass With {.ErrorMessage = message})
            response.StatusCode = HttpStatusCode.InternalServerError
            Return response
        End Function

        Public Shared Function FromException(ex As Exception) As ErrorResponse
            Dim response = New ErrorResponse(New ErrorClass With {.ErrorMessage = ex.Message, .FullException = ex.ToString()})
            response.StatusCode = HttpStatusCode.InternalServerError
            Return Response
        End Function
    End Class
End Namespace```
And then we need to make sure our Errors are now in that Response format. For that I will Cut into the OnError pipleine via our custom bootstrapper and add something to that pipleine. Like so.

```vbnet
Imports BecareServices.Constants
Imports BecareServices.ErrorPages
Imports Nancy.Bootstrapper
Imports Nancy.Elmah
Imports Nancy
Imports Nancy.Security
Imports BecareServices.BeCareApi.Session
Imports Nancy.ViewEngines

Public Class BootStrapper
    Inherits DefaultNancyBootstrapper

    Protected Overrides Sub ApplicationStartup(container As TinyIoc.TinyIoCContainer, pipelines As Nancy.Bootstrapper.IPipelines)
        MyBase.ApplicationStartup(container, pipelines)
        ConfigurePipelines(pipelines)
    End Sub

    Public Shared Sub ConfigurePipelines(pipelines As IPipelines)
        pipelines.OnError.AddItemToEndOfPipeline(Function(x, ex)
                                                     Log.ErrorException(ex.Message, ex)
                                                     Return ErrorResponse.FromException(ex)
                                                 End Function)
    End Sub

End Class
```
Now why did I not use a lambda there, I hear you think? I&#8217;ll come to that in a minute. I first have to adapt my customerrors class again. But only the handle method.

```vbnet
Public Sub Handle(statusCode As HttpStatusCode, context As NancyContext) Implements IStatusCodeHandler.Handle
            Dim errorPage As String
            If Not _errorPages.TryGetValue(statusCode, errorPage) Then
                Return
            End If
            If String.IsNullOrEmpty(errorPage) Then
                errorPage = "500"
            End If

            If statusCode = HttpStatusCode.NotFound Then
                context.Response = ErrorResponse.FromMessage("The resource you requested could not be found.").WithStatusCode(HttpStatusCode.NotFound)
            End If

            If context.Request.Headers.Accept.Count = 0 OrElse context.Request.Headers.Accept.Where(Function(x) x.Item1 = "text/html").Count = 1 Then
                Dim response = RenderView(context, errorPage, context.Response)
                response.StatusCode = statusCode
                context.Response = response
            Else
                Dim response = context.Response
                response.StatusCode = statusCode
                context.Response = response
            End If
        End Sub```
I now get html when asked or when the mediarange is empty and I get json in all other cases. At least now it is easy for the user of our services to see what the error was. 

Now why did I make that ugly extra method in the bootstrapper. 

Because I don&#8217;t want to write end to end tests to test if that works. I wanted to write some simpler tests to see if the above worked. 

And I did. And I wanted to make sure I got the same pipeline as in my implementation.

And here are a bunch of tests.

```vbnet
Imports BecareServices.ErrorPages
Imports FakeItEasy
Imports BecareServices.Test.Modules
Imports Nancy.Localization
Imports Nancy
Imports Nancy.Responses.Negotiation
Imports NUnit.Framework
Imports Nancy.Testing
Imports Nancy.ViewEngines.Razor

Namespace ErrorPages
    Public Class TestCustomErrors
        Private _browser As Browser
        
        &lt;SetUp()&gt;
        Public Sub FixtureSetup()
            Dim configuration = A.Fake(Of IRazorConfiguration)()
            Dim textResource = A.Fake(Of ITextResource)()
            Dim bootstrapper = New ConfigurableBootstrapper(Sub(config)
                                                                config.Module(Of ErrorModule)()
                                                                config.RootPathProvider(Of RootPathProvider)()
                                                                config.Dependency(Of ITextResource)(textResource)
                                                                config.ApplicationStartup(Sub(x, pipelines)
                                                                                              BeCareServices.BootStrapper.ConfigurePipelines(pipelines)
                                                                                          End Sub)
                                                                config.StatusCodeHandler(Of CustomErrors)()
                                                                config.ViewEngine(New RazorViewEngine(configuration))
                                                            End Sub)
            _browser = New Browser(bootstrapper)
        End Sub

        &lt;Test&gt;
        Public Sub IfGetErrorReturnsStatusCode500()
            Dim result = _browser.Get("/error", Sub(x)
                                                    x.HttpRequest()
                                                    x.Accept(New MediaRange() With {.Type = "application", .Subtype = "json"})
                                                End Sub)
            Assert.AreEqual(HttpStatusCode.InternalServerError, result.StatusCode)
        End Sub

        &lt;Test&gt;
        Public Sub IfGetErrorReturnsJsonWhenRequested()
            Dim result = _browser.Get("/error", Sub(x)
                                                    x.HttpRequest()
                                                    x.Accept(New MediaRange() With {.Type = "application", .Subtype = "json"})
                                                End Sub)
            Assert.IsTrue(result.Body.AsString.Contains("{""ErrorMessage"":""Testing"",""FullException"":"))
        End Sub

        &lt;Test&gt;
        Public Sub IfGetErrorReturnsViewWithErrorMessage()
            Dim result = _browser.Get("/error", Sub(x)
                                                    x.HttpRequest()
                                                End Sub)
            Assert.AreEqual("Testing", result.Body("p").ToList(2).InnerText)
        End Sub

        &lt;Test&gt;
        Public Sub IfGetNotFoundReturnsStatusCode404()
            Dim result = _browser.Get("/notfound", Sub(x)
                                                       x.HttpRequest()
                                                       x.Accept(New MediaRange() With {.Type = "application", .Subtype = "json"})
                                                   End Sub)
            Assert.AreEqual(HttpStatusCode.NotFound, result.StatusCode)
        End Sub

        &lt;Test&gt;
        Public Sub IfGetNotFoundReturnsJsonWhenRequested()
            Dim result = _browser.Get("/notfound", Sub(x)
                                                       x.HttpRequest()
                                                       x.Accept(New MediaRange() With {.Type = "application", .Subtype = "json"})
                                                   End Sub)
            Assert.IsTrue(result.Body.AsString.StartsWith("{""ErrorMessage"":""The resource you requested could not be found."",""FullException"":"))
        End Sub

        &lt;Test&gt;
        Public Sub IfGetNotFoundReturnsViewWithErrorMessage()
            Dim result = _browser.Get("/notfound", Sub(x)
                                                       x.HttpRequest()
                                                   End Sub)
            Assert.AreEqual("Page not found", result.Body("h2").ToList(0).InnerText)
        End Sub

        &lt;Test&gt;
        Public Sub IfGetMessageReturnsStatusCode500()
            Dim result = _browser.Get("/message", Sub(x)
                                                      x.HttpRequest()
                                                      x.Accept(New MediaRange() With {.Type = "application", .Subtype = "json"})
                                                  End Sub)
            Assert.AreEqual(HttpStatusCode.InternalServerError, result.StatusCode)
        End Sub

        &lt;Test&gt;
        Public Sub IfGetMessageReturnsJsonWhenRequested()
            Dim result = _browser.Get("/message", Sub(x)
                                                      x.HttpRequest()
                                                      x.Accept(New MediaRange() With {.Type = "application", .Subtype = "json"})
                                                  End Sub)
            Assert.IsTrue(result.Body.AsString.Contains("{""ErrorMessage"":""message"",""FullException"":"))
        End Sub

        &lt;Test&gt;
        Public Sub IfGetMessageReturnsViewWithErrorMessage()
            Dim result = _browser.Get("/message", Sub(x)
                                                      x.HttpRequest()
                                                  End Sub)
            Assert.AreEqual("message", result.Body("p").ToList(2).InnerText)
        End Sub

        &lt;Test&gt;
        Public Sub IfGetFromExceptionReturnsStatusCode500()
            Dim result = _browser.Get("/fromexception", Sub(x)
                                                            x.HttpRequest()
                                                            x.Accept(New MediaRange() With {.Type = "application", .Subtype = "json"})
                                                        End Sub)
            Assert.AreEqual(HttpStatusCode.InternalServerError, result.StatusCode)
        End Sub

        &lt;Test&gt;
        Public Sub IfGetFromExceptionReturnsJsonWhenRequested()
            Dim result = _browser.Get("/fromexception", Sub(x)
                                                            x.HttpRequest()
                                                            x.Accept(New MediaRange() With {.Type = "application", .Subtype = "json"})
                                                        End Sub)
            Assert.IsTrue(result.Body.AsString.Contains("{""ErrorMessage"":""fromexception"",""FullException"":"))
        End Sub

        &lt;Test&gt;
        Public Sub IfGetFromExceptionReturnsViewWithErrorMessage()
            Dim result = _browser.Get("/fromexception", Sub(x)
                                                            x.HttpRequest()
                                                        End Sub)
            Assert.AreEqual("fromexception", result.Body("p").ToList(2).InnerText)
        End Sub

        Private Class ErrorModule
            Inherits NancyModule

            Public Sub New()
                [Get]("/error") = Function(parameters)
                                      Throw New Exception("Testing")
                                  End Function
                [Get]("/notfound") = Function(parameters)
                                         Return HttpStatusCode.NotFound
                                     End Function
                [Get]("/message") = Function(parameters)
                                        Return ErrorResponse.FromMessage("message")
                                    End Function
                [Get]("/fromexception") = Function(parameters)
                                              Return ErrorResponse.FromException(New Exception("fromexception"))
                                          End Function
            End Sub

        End Class

    End Class

    
End Namespace```
It&#8217;s not extremely pretty but it works. And all tests pass. 

Go ahead and tell me I&#8217;m doing it wrong.

 [1]: /index.php/WebDev/ServerProgramming/nancy-and-custom-error-pages
 [2]: http://mike-ward.net/blog/post/00824/custom-error-pages-in-nancyfx
 [3]: http://paulstovell.com/blog/consistent-error-handling-with-nancy