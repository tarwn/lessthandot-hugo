---
title: 'Nancy and localization: The cookie approach'
author: Christiaan Baes (chrissie1)
type: post
date: 2015-02-26T10:53:22+00:00
ID: 3268
url: /index.php/uncategorized/nancy-and-localization-the-cookie-approach/
views:
  - 779
categories:
  - Uncategorized

---
## Introduction

So in my previous post I showed you how localization worked in Nancy and I showed you I set the Context.Culture to tell Nancy which culture to use. I might be doing this all wrong but that worked for me. But setting the context on each request will get boring if you have a 100 modules. So here is how I solved that for my use case. 

## The cookie approach

For my use case the person viewing my website has no login, he surfs the site anonymously. So the best way in my mind to keep his language setting is to keep it in a cookie. 

So I created a module for that.

```vbnet
Imports System.Globalization
Imports Nancy

Namespace Modules
    Public Class HomeModule
        Inherits NancyModule

        Public Sub New()
            [Get]("/") = Function(parameters)
                              context.Culture = New CultureInfo(context.Request.Cookies("culture"))
                              Return View("Home")
                         End Function
            [Get]("/{locale}") = Function(parameters)
                                     Try
                                         Context.Culture = New CultureInfo(parameters.locale.ToString)
                                     Catch ex As Exception
                                         Context.Culture = New CultureInfo("en-US")
                                     End Try
                                     Return View("Home").WithCookie(New Cookies.NancyCookie("culture", Context.Culture.Name))
                                 End Function
        End Sub

    End Class
End Namespace
```
And I adapted my view a little.

```html
@Code
    Layout = Nothing
End Code






    

<div>
  <a href="\nl-BE">Nederlands</a>
          <a href="\en-US">English</a>
          
  
  <p>
    @Text.Home.MyString
  </p>
      
</div>


```

But as you can see I am still setting the context.Culture in each request, I&#8217;m just reading it from the cookie this time.

No need for that. I can do that in the bootstrapper.

So you create a Bootstrapper.vb file in the root of your project and 

```vbnet
Imports Nancy.Bootstrapper
Imports Nancy

Public Class BootStrapper
    Inherits DefaultNancyBootstrapper
    
    Protected Overrides Sub RequestStartup(container As TinyIoc.TinyIoCContainer, pipelines As IPipelines, context As NancyContext)
        MyBase.RequestStartup(container, pipelines, context)
        If context.Request.Cookies.ContainsKey("culture") Then
            context.Culture = New Globalization.CultureInfo(context.Request.Cookies("culture"))
        End If
    End Sub
    
End Class
```
And now your HomeModule will look like this.

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
                                     Return View("Home").WithCookie(New Cookies.NancyCookie("culture", Context.Culture.Name))
                                 End Function
        End Sub

    End Class
End Namespace
```
And now all requests will have their culture set the same way.

> Interestingly Internet explorer 11 on windows 7 seemed to give me a culture cookie even if I did not set it explicitly and it was filled with the culture that windows was using at that time. On chrome it did not have a cookie.