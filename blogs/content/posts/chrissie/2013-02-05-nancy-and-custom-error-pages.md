---
title: Nancy and custom error pages.
author: Christiaan Baes (chrissie1)
type: post
date: 2013-02-05T09:59:00+00:00
ID: 1969
excerpt: I added some custom error pages to my Nancy website.
url: /index.php/webdev/serverprogramming/nancy-and-custom-error-pages/
views:
  - 6071
categories:
  - ASP.NET
  - Server Programming
tags:
  - nancy
  - vb.net

---
## Introduction

I wanted to show my users some prettier errorpages then the default you get from IIS or [Nancy][1].

This being the default for Nancy. Because I know you guys like pictures.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/nancy/error/NancyError1.png?mtime=1360064701"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/nancy/error/NancyError1.png?mtime=1360064701" width="552" height="285" /></a>
</div>

Of course this was the first time ever in any webapp that I did that, So I had to look certain things up. And [I thought this][2] was pretty cool.

But of course that&#8217;s not how Nancy works.

Nancy is a Lady, so we treat her like one.

Setting it up.

So first of we will have to change our webconfig so that it servers us custom errors.

You do this by adding the following.

```xml
&lt;configuration&gt;
  &lt;system.webServer&gt;
    &lt;httpErrors errorMode="Custom" existingResponse="PassThrough" /&gt;
  &lt;/system.webServer&gt;
&lt;/configuration&gt;```
Next up is to make a few html file that we can serve as our custom pages.

Something like this.

```html
&lt;!DOCTYPE html&gt;
&lt;html&gt;
&lt;head&gt;
    &lt;title&gt;404&lt;/title&gt;
&lt;/head&gt;
&lt;body&gt;
    &lt;p&gt;Special Chrissie 404 page&lt;/p&gt;
&lt;/body&gt;
&lt;/html&gt;
```
I made a similar one for 500.

I named them 404.html and 500.html and made sure the properties of the files were set to embedded resource.

Now I just had to create a custom IStatusCodeHandler, like this.

```vbnet
Imports System.IO
Imports Nancy
Imports Nancy.ErrorHandling

Public Class CustomErrors
    Implements IStatusCodeHandler

    Private ReadOnly _errorPages As IDictionary(Of HttpStatusCode, String)
    Private ReadOnly _supportedStatusCodes As HttpStatusCode() = {HttpStatusCode.NotFound, HttpStatusCode.InternalServerError}


    Public Sub New()
        _errorPages = New Dictionary(Of HttpStatusCode, String) From
            {
                {HttpStatusCode.NotFound, LoadResource("404.html")},
                {HttpStatusCode.InternalServerError, LoadResource("500.html")}
            }

    End Sub

    Public Sub Handle(statusCode As HttpStatusCode, context As NancyContext) Implements IStatusCodeHandler.Handle
        If context.Response IsNot Nothing AndAlso context.Response.Contents IsNot Nothing AndAlso Not ReferenceEquals(context.Response.Contents, Response.NoBody) Then
            Return
        End If

        Dim errorPage As String

        If Not _errorPages.TryGetValue(statusCode, errorPage) Then
            Return
        End If

        If String.IsNullOrEmpty(errorPage) Then
            Return
        End If

        ModifyResponse(statusCode, context, errorPage)
    End Sub

    Public Function HandlesStatusCode(statusCode As HttpStatusCode, context As NancyContext) As Boolean Implements IStatusCodeHandler.HandlesStatusCode
        Return _supportedStatusCodes.Any(Function(s) s = statusCode)
    End Function

    Private Shared Sub ModifyResponse(statusCode As HttpStatusCode, context As NancyContext, errorPage As String)

        If context.Response Is Nothing Then
            context.Response = New Response() With {.StatusCode = statusCode}
        End If

        context.Response.ContentType = "text/html"
        context.Response.Contents = Sub(s)
                                        Using writer = New StreamWriter(s, Encoding.UTF8)
                                            writer.Write(errorPage)
                                        End Using
                                    End Sub
    End Sub

    Private Shared Function LoadResource(filename As String) As String
        Dim resourceStream = GetType(CustomErrors).Assembly.GetManifestResourceStream(String.Format("WebApplication3.{0}", filename))
        If resourceStream Is Nothing Then
            Return String.Empty
        End If
        Using reader = New StreamReader(resourceStream)
            Return reader.ReadToEnd()
        End Using
    End Function


End Class
```
Normally one would expect Nancy to pick this file up, because Nancy is a good girl. (One of the many benefits of giving your framework a woman&#8217;s name is all the nice cliches bloggers can use, Thank you for that) (One of the disadvantages is that Google has a dirty mind.)

But Nancy has a bug, so you have to register it yourself. You can do this in the bootstrapper. I [logged the bug in the github issuetracker][3] so this might be resolved by the time you want to use this. <span class="MT_red">For your information, this bug was fixed 3 hours after logging it.</span> 

```vbnet
Imports Nancy.Bootstrapper

    Public Class MyBootStrapper
        Inherits Nancy.DefaultNancyBootstrapper

        Protected Overrides ReadOnly Property InternalConfiguration As NancyInternalConfiguration
            Get
                Return NancyInternalConfiguration.WithOverrides(Sub(b)
                                                                    b.StatusCodeHandlers = New List(Of Type) From {GetType(CustomErrors)}
                                                                End Sub)
            End Get
        End Property

    End Class
```
I&#8217;m sure I killed a few puppies along the way. But it works. And here is the smoking gun.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/nancy/error/NancyError2.png?mtime=1360065383"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/nancy/error/NancyError2.png?mtime=1360065383" width="193" height="62" /></a>
</div>

Pretty, no?

I have to thank [Steven Robbins][4] and [Phillip Haydon][5] for their kind and generous help.

And you can make it even better by using a masterpage. 

Just create a Master.html.

```html
&lt;!DOCTYPE html&gt;
&lt;html&gt;
&lt;head&gt;
    &lt;title&gt;[Title]&lt;/title&gt;
    &lt;link href="/Content/Css/main.css" rel="stylesheet" /&gt;
&lt;/head&gt;
&lt;body&gt;
    [Details]
&lt;/body&gt;
&lt;/html&gt;```
And see how I reference the css in my content folder.

And then you can adapt the 404 and 500 page.

```html
&lt;h2&gt;Page not found&lt;/h2&gt;
&lt;p&gt;We could not find the page you were looking for. Are you sure this is where you left it? This error is logged but I'm pretty sure the administrator can not fix it.&lt;/p&gt;
&lt;p&gt;If it is urgent you can contact you admin by shouting real loud.&lt;/p&gt;
&lt;p&gt;BTW: You can find a link in the footer to the logfiles if you want to fix the error yourself.&lt;/p&gt;

```
Now we go back to our CustomErrors class. And change the LoadResource method to this.

```vbnet
Private Shared Function LoadResource(ByVal filename As String, ByVal httpStatusCode As HttpStatusCode) As String
        Dim masterPageStream = GetType(CustomErrors).Assembly.GetManifestResourceStream(String.Format("BeCare_Server.{0}", "Master.html"))
        Dim masterPage As String
        Using reader = New StreamReader(masterPageStream)
            masterPage = reader.ReadToEnd()
        End Using
        masterPage = masterPage.Replace("[Title]", String.Format("Error {0}", httpStatusCode.ToString("D")))
        Dim resourceStream = GetType(CustomErrors).Assembly.GetManifestResourceStream(String.Format("BeCare_Server.{0}", filename))
        If resourceStream Is Nothing Then
            Return String.Empty
        End If
        Dim details As String
        Using reader = New StreamReader(resourceStream)
            details = reader.ReadToEnd()
        End Using
        masterPage = masterPage.Replace("[Details]", details)
        Return masterPage
    End Function```
And you can change your Handle method to this.

```vbnet
Public Sub Handle(statusCode As HttpStatusCode, context As NancyContext) Implements IStatusCodeHandler.Handle
        If context.Response IsNot Nothing AndAlso context.Response.Contents IsNot Nothing AndAlso Not ReferenceEquals(context.Response.Contents, Response.NoBody) Then
            Return
        End If

        Dim errorPage As String

        If Not _errorPages.TryGetValue(statusCode, errorPage) Then
            Return
        End If

        If String.IsNullOrEmpty(errorPage) Then
            errorPage = LoadResource("500.html", statusCode)
        End If

        ModifyResponse(statusCode, context, errorPage)
    End Sub```
So that now every statuscode in the known world is handled by your custom errorpages. Of course this is optional.

And now you get pretty page.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/nancy/error/NancyError3.png?mtime=1360074055"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/nancy/error/NancyError3.png?mtime=1360074055" width="843" height="751" /></a>
</div>

Cooooooooooollll!!!

 [1]: http://nancy.org
 [2]: http://www.asp.net/web-forms/tutorials/deployment/deploying-web-site-projects/displaying-a-custom-error-page-cs
 [3]: https://github.com/NancyFx/Nancy/issues/951
 [4]: https://twitter.com/Grumpydev
 [5]: https://twitter.com/philliphaydon