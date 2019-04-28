---
title: 'SignalR and VB.Net: Hubs and objects'
author: Christiaan Baes (chrissie1)
type: post
date: 2012-11-11T07:05:00+00:00
ID: 1785
excerpt: How to use SignalR with hubs and returning an Object.
url: /index.php/webdev/serverprogramming/signalr-and-vb-net-hubs/
views:
  - 15788
categories:
  - ASP.NET
  - Server Programming
tags:
  - signalr
  - vb.net

---
## Introduction

Yesterday I wrote [a blog about SignalR][1] and just 5 seconds after posting it I got this on twitter.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/SignalR/SignalR7.png?mtime=1352622942"><img alt="" src="/wp-content/uploads/users/chrissie1/SignalR/SignalR7.png?mtime=1352622942" width="299" height="125" /></a>
</div>

So, here I am on a Sunday morning writing about [SignalR and Hubs][2].

Why hubs? You ask.

> Hubs provide a higher level RPC framework over a PersistentConnection. If you have different types of messages that you want to send between server and client then hubs is recommended so you don&#8217;t have to do your own dispatching.

It took me a while to figure this out, but I got there in the end.

## The server

Setting up the server should have been easy. But it lacked something. 

So watch out if you are working with VB.Net.

First I started to create my hub.

```vbnet
Imports Microsoft.AspNet.SignalR.Hubs

Public Class Plants
    Inherits Hub

    Dim _plants As IList(Of Plant)

    Public Sub New()
        _plants = New List(Of Plant)
        _plants.Add(New Plant With {.Id = 1, .Genus = "Fagus", .Species = "Sylvatica", .CommonNames = {"Common Beech"}})
    End Sub

    Public Function GetPlant(id As Integer) As Plant
        Return _plants.Where(Function(x) x.Id = id).FirstOrDefault
    End Function

End Class
```
This hub should return a plant for the id given or nothing when the id is not found.

If you start this you should get the url to your server.

if you browse to that url and add /signalr/hubs to it you should get something like this.

> /*!
   
> * ASP.NET SignalR JavaScript Library v1.0.0
   
> * http://signalr.net/
   
> *
   
> * Copyright Microsoft Open Technologies, Inc. All rights reserved.
   
> * Licensed under the Apache 2.0
   
> * https://github.com/SignalR/SignalR/blob/master/LICENSE.md
   
> *
   
> */
> 
> /// <reference path="....SignalR.Client.JSScriptsjquery-1.6.2.js">
  
> /// <reference path="jquery.signalR.js">
  
> (function ($, window) {
      
> /// 
> 
> <param name="$" type="jQuery" />
> &#8220;u</reference></reference>

But I did not get that. I got a 404 not found page instead. Which isn&#8217;t good.

And it was caused by the routes not being registered. Which normally happens via this cs file that is in your App_Start folder (which was created by the nuget package).

<!-- codeblock lang="csharp" line="1" -->

<pre>.Web.Routing;
using Microsoft.AspNet.SignalR;
using Microsoft.AspNet.SignalR.Hosting.AspNet;

[assembly: PreApplicationStartMethod(typeof(SignalRTesting.RegisterHubs), "Start")]

namespace SignalRTesting
{
    public static class RegisterHubs
    {
        public static void Start()
        {
            // Register the default hubs route: ~/signalr/hubs
            RouteTable.Routes.MapHubs();            
        }
    }
}
</pre>

<!-- /codeblock -->

And normally you don&#8217;t need to setup any roots in your Global.asax. 

But when I did. It worked.

So add a Global.asax and change it to this.

```vbnet
Imports System.Web.SessionState
Imports System.Web.Routing
Imports Microsoft.AspNet.SignalR

Public Class Global_asax
    Inherits System.Web.HttpApplication

    Sub Application_Start(ByVal sender As Object, ByVal e As EventArgs)
        RouteTable.Routes.MapHubs()
    End Sub


End Class```
And now your server should return the correct page on browsing to /signalr/hubs

## The client

First thing to try is to make a console client.

Look at the code of our server and see that we return an object of type Plant.

Our Plant object looks like this BTW.

```vbnet
Public Class Plant
    Public Property Id As Integer
    Public Property Genus As String
    Public Property Species As String
    Public Property CommonNames As String()
End Class```
The return means that only the caller will get that returnvalue, not any of the other clients.

And here is such a client.

```vbnet
Imports Microsoft.AspNet.SignalR.Client.Hubs
Imports Microsoft.AspNet.SignalR.Client

Module Module1

    Sub Main()
        Dim connection = New HubConnection("http://localhost:50865")

        Dim plants = connection.CreateHubProxy("Plants")

        connection.Start().Wait()

        Dim line As String = Nothing
        Console.WriteLine("type the id + enter to send, type exit + enter to exit.")
        While line &lt;&gt; "exit"
            line = Console.ReadLine()
            plants.Invoke(Of Object)("GetPlant", line).ContinueWith(Sub(data)
                                                                        If data.Result IsNot Nothing Then
                                                                            Console.WriteLine(data.Result.ToString)
                                                                        Else
                                                                            Console.WriteLine("No plant with that id.")
                                                                        End If
                                                                    End Sub)
        End While
    End Sub

End Module
```
And if you type 1 you should get this in return.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/SignalR/SignalR8.png?mtime=1352624162"><img alt="" src="/wp-content/uploads/users/chrissie1/SignalR/SignalR8.png?mtime=1352624162" width="549" height="216" /></a>
</div>

But we could have just use [ServiceStack][3] if we needed something like that. 

So what is the point? 

Let&#8217;s just say that we want to inform all our clients that someone just requested a plant with id 1 than we could do this instead.

Change the server code and start it up.

```vbnet
Imports Microsoft.AspNet.SignalR.Hubs

Public Class Plants
    Inherits Hub

    Dim _plants As IList(Of Plant)

    Public Sub New()
        _plants = New List(Of Plant)
        _plants.Add(New Plant With {.Id = 1, .Genus = "Fagus", .Species = "Sylvatica", .CommonNames = {"Common Beech"}})
    End Sub

    Public Function GetPlant(id As Integer) As Plant
        Clients.All.addMessage("Someone requested id: " & id)
        Return _plants.Where(Function(x) x.Id = id).FirstOrDefault
    End Function

End Class
```
As you can see I use Clients.All.addMessage to send a message to all Clients with the id that was requested.

Change the console client to this.

```vbnet
Imports Microsoft.AspNet.SignalR.Client.Hubs

Module Module1

    Sub Main()
        Dim connection = New HubConnection("http://localhost:50865")

        Dim plants = connection.CreateHubProxy("Plants")

        plants.On(Of String)("addMessage", Sub(data) Console.WriteLine(data.ToString()))

        connection.Start().Wait()

        Dim line As String = Nothing
        Console.WriteLine("type the id + enter to send, type exit + enter to exit.")
        While line &lt;&gt; "exit"
            line = Console.ReadLine()
            plants.Invoke(Of Object)("GetPlant", line).ContinueWith(Sub(data)
                                                                        If data.Result IsNot Nothing Then
                                                                            Console.WriteLine(data.Result.ToString)
                                                                        Else
                                                                            Console.WriteLine("No plant with that id.")
                                                                        End If
                                                                    End Sub)
        End While
    End Sub

End Module
```
I just added the plants.On method to intercept the addMessage events.

And now when I open multiple clients I get this.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/SignalR/SignalR9.png?mtime=1352624644"><img alt="" src="/wp-content/uploads/users/chrissie1/SignalR/SignalR9.png?mtime=1352624644" width="506" height="347" /></a>
</div>

Which is cool.

## Conclusion

Hubs are what you will mostly use when you use SignalR.

 [1]: /index.php/WebDev/ServerProgramming/ASPNET/signalr-and-vb-net
 [2]: https://github.com/SignalR/SignalR/wiki/Hubs
 [3]: http://www.servicestack.net/