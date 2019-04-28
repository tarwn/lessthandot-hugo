---
title: Nancy and adding the windows authenticated user to your page.
author: Christiaan Baes (chrissie1)
type: post
date: 2013-02-04T11:49:00+00:00
ID: 1965
excerpt: Putting the windows authenticated user on your webpage when using Nancy.
url: /index.php/webdev/serverprogramming/nancy-and-adding-the-windows/
views:
  - 5228
categories:
  - ASP.NET
  - Server Programming
tags:
  - 'c#'
  - nancy
  - vb.net
  - windows authentication.

---
So I have been using Nancy and my IIS is configured to use windows authentication. 

Now how do I get the current authenticated user onto my webpage.

But first of all how do I get the name of the current authenticated user. 

Well that was the simple part.

```vbnet
HttpContext.Current.User.Identity.Name```
And there you have it.

Now we all know that we can do this in Nancy with Razor to get the current logged in user when using formsauthentication or basic authentication.

```vbnet
Url.RenderContext.Context.CurrentUser```
Of course we could do this in our Module.

```vbnet
Context.CurrentUser = New User With {.UserName = HttpContext.Current.User.Identity.Name}```
And that would work, but it would become very tedious to repeat this for every module we have. So we have to look for a better way of doing this. And of course there is.

We can make our own bootstrapper and change it in the nancy pipleine.

```vbnet
Imports Nancy
Imports Nancy.Security

Public Class BootStrapper
    Inherits DefaultNancyBootstrapper

    Protected Overrides Sub ApplicationStartup(container As TinyIoc.TinyIoCContainer, pipelines As Nancy.Bootstrapper.IPipelines)
        MyBase.ApplicationStartup(container, pipelines)
        pipelines.AfterRequest.AddItemToEndOfPipeline(Sub(ctx)
                                                          ctx.CurrentUser = New User With {.UserName = HttpContext.Current.User.Identity.Name}
                                                      End Sub)
    End Sub

    Private Class User
        Implements IUserIdentity

        Public Property Claims As IEnumerable(Of String) Implements IUserIdentity.Claims

        Public Property UserName As String Implements IUserIdentity.UserName
    End Class

End Class```
So I added this to the AfterRequest hook And that changes the currentuser on every request for me.

I also created a class User because CurrentUser needs a class that implements IUserIdentity. 

Cool huh.