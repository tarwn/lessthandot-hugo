---
title: Nancy and VB.Net
author: Christiaan Baes (chrissie1)
type: post
date: 2012-12-11T10:12:00+00:00
ID: 1847
excerpt: Nancy is a webframework for .Net. And I will try to use it with VB.Net.
url: /index.php/webdev/serverprogramming/nancy-and-vb-net/
views:
  - 4058
categories:
  - ASP.NET
  - Server Programming
tags:
  - nancy
  - vb.net

---
## Introduction

After having tried out [Servicestack][1] it is time to try [Nancy][2].

> Nancy is a lightweight, low-ceremony, framework for building HTTP based services on .Net and Mono. The goal of the framework is to stay out of the way as much as possible and provide a super-duper-happy-path to all interactions.

Nancy has some [nice docs][3] and I like that.

And Nancy has a nice ecosystem and dedicated masters.

## Hello world

Hello world should be easy enough. Just create an empty asp.net project and then add the nuget package. And some code.

As you can see Nancy has a buttload of packages on nuget.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/nancy/nancy1.png?mtime=1355226559"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/nancy/nancy1.png?mtime=1355226559" width="900" height="852" /></a>
</div>

We pick the Nancy.Hosting.AspNet for this one.

Interesting to note that there is also a self hosting one. I could have used that one for the easyhttp testing maybe.

That package adds this web.config for you.

```xml
&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;!--
  For more information on how to configure your ASP.NET application, please visit
  http://go.microsoft.com/fwlink/?LinkId=169433
  --&gt;
&lt;configuration&gt;
  &lt;system.web&gt;
    &lt;compilation debug="true" strict="false" explicit="true" targetFramework="4.5" /&gt;
    &lt;httpRuntime targetFramework="4.5" /&gt;
    &lt;httpHandlers&gt;
      &lt;add verb="*" type="Nancy.Hosting.Aspnet.NancyHttpRequestHandler" path="*" /&gt;
    &lt;/httpHandlers&gt;
  &lt;/system.web&gt;
  &lt;system.webServer&gt;
    &lt;validation validateIntegratedModeConfiguration="false" /&gt;
    &lt;handlers&gt;
      &lt;add name="Nancy" verb="*" type="Nancy.Hosting.Aspnet.NancyHttpRequestHandler" path="*" /&gt;
    &lt;/handlers&gt;
  &lt;/system.webServer&gt;
&lt;/configuration&gt;```
Now add a Class and inherit it from NancyModule.

```vbnet
Imports Nancy

Public Class HelloModule
    Inherits NancyModule

    Public Sub New()
        MyBase.Get("/") = Function(parameters) "Hello World"
    End Sub

End Class```
Run it and see the magic.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/nancy/nancy2.png?mtime=1355227123"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/nancy/nancy2.png?mtime=1355227123" width="196" height="167" /></a>
</div>

In essence you are looking at the root route and send it hello world when that route is requested.

So when I try another route than just http://localhost/ I will get a 404.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/nancy/nancy3.png?mtime=1355227555"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/nancy/nancy3.png?mtime=1355227555" width="511" height="258" /></a>
</div>

Cool. 

Now I can just add that route, for instance http://localhost/plant

```vbnet
Imports Nancy

Public Class HelloModule
    Inherits NancyModule

    Public Sub New()
        MyBase.Get("/") = Function(parameters) "Hello World"
        MyBase.Get("/plant") = Function(parameters) "Hello Plant"
    End Sub

End Class```
you will get this.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/nancy/nancy4.png?mtime=1355227810"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/nancy/nancy4.png?mtime=1355227810" width="269" height="244" /></a>
</div>

Now I know you should put each route into it&#8217;s own class, but spaghetti code is so much nicer.

## Conclusion

It took me all of five minutes to get started, mainly due to the not being able to copy paste code. But it works as advertised. Maybe in the next post I will try the Self thing for the easyhttp tests.

 [1]: http://servicestack.net/
 [2]: http://nancyfx.org/
 [3]: https://github.com/NancyFx/Nancy/wiki/Documentation