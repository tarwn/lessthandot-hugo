---
title: Trying out Gibraltar’s Loupe with Nancy
author: Christiaan Baes (chrissie1)
type: post
date: 2013-03-23T03:55:00+00:00
ID: 2051
excerpt: "Trying out Gibraltar's loupe with a Nancy application."
url: /index.php/webdev/serverprogramming/trying-out-gibraltar-s-loupe/
views:
  - 14401
categories:
  - ASP.NET
  - Server Programming
tags:
  - "gibraltar's loupe"

---
## Introduction

I downloaded and tested Gibraltar&#8217;s Loupe today and tried to find out what it could add to my application. And because Rachel Hawley asked me t review it, and who am I to refuse her anything.

I already add as much logging to my application as possible because it makes it a lot easier to find bugs and fix them once you go into productions. 

But log files can get overwhelming sometimes.

This is where Gibraltar analyst can help. 

## Installation

I downloaded the 3.0 preview 3. Installation is easy and for now I only installed the desktopversion, but there is also a webversion.

I created a small nancy application with the razor viewengine.

With this as my view.

```html
&lt;!DOCTYPE html&gt;
&lt;html xmlns="http://www.w3.org/1999/xhtml"&gt;
&lt;head&gt;
    &lt;title&gt;&lt;/title&gt;
&lt;/head&gt;
&lt;body&gt;
    &lt;p&gt;Welcome to OUR world!&lt;/p&gt;
&lt;/body&gt;
&lt;/html&gt;
```
and this as my module.

```vbnet
Imports Nancy
Imports Gibraltar.Agent

Public Class MainModule
    Inherits NancyModule

    Public Sub New()
        MyBase.Get("/") = Function(parameters)
                              Return View("Main")
                          End Function
    End Sub
End Class```
I saved all of it and then went into the loupe desktop.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/Loupe/loupe1.png?mtime=1364009122"><img alt="" src="/wp-content/uploads/users/chrissie1/Loupe/loupe1.png?mtime=1364009122" width="1032" height="652" /></a>
</div>

YOu can now click the big green button that says Add Gribraltar now, point it to your project give it a name and whatnot and voila Gibraltar is there.

## Logging

When I run it and go to live sessions.
  
I can see it. 

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/Loupe/loupe3.png?mtime=1364009498"><img alt="" src="/wp-content/uploads/users/chrissie1/Loupe/loupe3.png?mtime=1364009498" width="1032" height="652" /></a>
</div>

No idea why it is there twice but you can click one of them.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/Loupe/loupe2.png?mtime=1364009376"><img alt="" src="/wp-content/uploads/users/chrissie1/Loupe/loupe2.png?mtime=1364009376" width="991" height="524" /></a>
</div>

That&#8217;s already more information than we are used to, and we have yet to add logging.

We can do this by using the Gibraltar Agent logging (but we don&#8217;t have to).

Just add a line like this.

```vbnet
MyBase.Get("/") = Function(parameters)
                              Log.TraceInformation("Opening mainmodule")
                              Return View("Main")
                          End Function```
And next time you run this.

you will see this in your logging session.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/Loupe/loupe4.png?mtime=1364009881"><img alt="" src="/wp-content/uploads/users/chrissie1/Loupe/loupe4.png?mtime=1364009881" width="1101" height="187" /></a>
</div>

You can even click on that link and go to your code.

Or see the code in the tab mainmodule.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/Loupe/loupe5.png?mtime=1364010034"><img alt="" src="/wp-content/uploads/users/chrissie1/Loupe/loupe5.png?mtime=1364010034" width="652" height="249" /></a>
</div>

That is simple enough.

## Exceptions

So how do we deal with exceptions and unhandled exceptions?

Let&#8217;s add this code to our module.

```vbnet
MyBase.Get("/test") = Function(parameters)
                                  Try
                                      Throw New Exception("some message loup should pick up")
                                  Catch ex As Exception
                                      Log.TraceError(ex, "something")
                                      Return View("Main")
                                  End Try
                              End Function
        MyBase.Get("/test2") = Function(parameters)
                                       Throw New Exception("some message loup should pick up")
                               End Function```
If I now go to the page /test I will see this.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/Loupe/loupe6.png?mtime=1364010367"><img alt="" src="/wp-content/uploads/users/chrissie1/Loupe/loupe6.png?mtime=1364010367" width="813" height="199" /></a>
</div>

The exception is nicely logged.

But if I go to page /test2, I see nothing.

Gibraltar does not log unhandled exception automatically. We either need ELMAH or we need to add logging to an event somewhere.

Luckily in Nancy this is easy.

Just add a class that implements IErrorhandler and Nancy will do the rest.

```vbnet
Imports Nancy
Imports Nancy.ErrorHandling
Imports Gibraltar.Agent

Public Class Errorhandler
    Implements IErrorHandler

    Public Function HandlesStatusCode(ByVal statusCode As HttpStatusCode, ByVal context As NancyContext) As Boolean Implements IStatusCodeHandler.HandlesStatusCode
        Return statusCode = HttpStatusCode.InternalServerError
    End Function

    Public Sub Handle(ByVal statusCode As HttpStatusCode, ByVal context As NancyContext) Implements IStatusCodeHandler.Handle
        Dim errorObject As Object
        context.Items.TryGetValue(NancyEngine.ERROR_EXCEPTION, errorObject)
        Dim ex = CType(errorObject, Exception)
        Log.TraceError(ex, "unhandled exception")
    End Sub
End Class```
And now we will see our exception.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/Loupe/loupe7.png?mtime=1364010747"><img alt="" src="/wp-content/uploads/users/chrissie1/Loupe/loupe7.png?mtime=1364010747" width="798" height="286" /></a>
</div>

## Conclusion

Loupe was pretty easy to setup and get going. Loupe does however come at a price. Is that price to high? That depends on the value you place on your business.