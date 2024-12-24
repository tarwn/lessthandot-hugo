---
title: SignalR and VB.Net
author: Christiaan Baes (chrissie1)
type: post
date: 2012-11-10T08:35:00+00:00
ID: 1784
excerpt: "SignalR is an Async library for .NET to help build real-time, multi-user interactive web applications. And I'm gonna take it for a spin. Because that is what people do on a Saturday."
url: /index.php/webdev/serverprogramming/aspnet/signalr-and-vb-net/
views:
  - 33908
categories:
  - ASP.NET
  - Javascript
  - XHTML and CSS
tags:
  - signalr
  - vb.net

---
## Introduction

If you haven&#8217;t heard of [SignalR][1] then you have probably been living under a rock for the past year or so.

> SignalR is an Async library for .NET to help build real-time, multi-user interactive web applications.

In other words, it&#8217;s kind of a client-servery thing.

So what can we do with it. We can send messages to all the clients that are connected to our server for one. 

Let&#8217;s try this. 

For this post I took [the quickstart example on the SignalR wiki][2].

## The server

First thing to do is to setup the server.

For this I created an Empty ASP.Net web Application. 

Then I did the Nuget thing.

> Install-Package Microsoft.AspNet.SignalR -pre

Then create a Global.asax file.

```vbnet
Imports System.Web.SessionState
Imports System.Web.Routing
Imports Microsoft.AspNet.SignalR

Public Class Global_asax
    Inherits System.Web.HttpApplication

    Sub Application_Start(ByVal sender As Object, ByVal e As EventArgs)
        RouteTable.Routes.MapConnection(Of MyConnection)("echo", "echo/{*operation}")
    End Sub


End Class```
this will create the routes that will tell you where you have to go to get your information. As you can see the route echo will use our MyConnection class which we will create next.

```vbnet
Imports Microsoft.AspNet.SignalR
Imports System.Threading.Tasks

Public Class MyConnection
    Inherits PersistentConnection

    Protected Overrides Function OnReceivedAsync(equest As IRequest, connectionId As String, data As String) As Task
        Return Connection.Broadcast(data)
    End Function

End Class
```
This is the most basic form. Take the data you recieved and broadcast it to all clients. 

Start this project and you should see something like this.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/SignalR/SignalR1.png?mtime=1352541992"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/SignalR/SignalR1.png?mtime=1352541992" width="688" height="369" /></a>
</div>

Which at least means our server has started. 

Take note of the url you see in your addressbar and remember that for the next session.

## The console client

The first thing to do after this is to make a consoleapplication to test if it really works.

Go to your nuget console and copy paste this in. Make sure the nuget console is in the right project first.

> Install-Package -pre Microsoft.AspNet.SignalR.Client

And here is my slightly improved version of that console app.

```vbnet
Imports Microsoft.AspNet.SignalR.Client

Module Module1

    Sub Main()
        Dim connection = New Connection("http://localhost:50865/echo")

        AddHandler connection.Received, Sub(data) Console.WriteLine(data)

        connection.Start().Wait()

        Dim line As String = Nothing
        Console.WriteLine("type anything + enter to send, type exit + enter to exit.")
        While line &lt;&gt; "exit"
            line = Console.ReadLine()
            connection.Send(line).Wait()
        End While
    End Sub

End Module
```
Make sure you use the correct url. You know, the one I told you to remember.
  
This will show you the incoming messages via a console.writeline using an AddHandler of the Recieved event of the connection.

And it will give you the possibility to send messages.

Build it and go to your output folder where you click on the exe file several times to get several windows open at the same time and start typing in one of them.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/SignalR/SignalR2.png?mtime=1352542739"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/SignalR/SignalR2.png?mtime=1352542739" width="1186" height="473" /></a>
</div>

You can see the window I have typed in has the message twice and the other clients will have it once. 

Now that was easy.

## The webclient

It is just as easy to create the weblient.

Just create another Empty ASP.Net web application and create an empty index.html file in that.

Then do the nuget thing again.

And make sure it created a Scripts folder with 5 js files in it.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/SignalR/SignalR3.png?mtime=1352543078"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/SignalR/SignalR3.png?mtime=1352543078" width="273" height="151" /></a>
</div>

The important file here is jquery.signalR-1.0.0-alpha1.min.js We will need that soon.

Now copy the following code into the index.html file.

```html
&lt;!DOCTYPE html&gt;
&lt;html xmlns="http://www.w3.org/1999/xhtml"&gt;
&lt;head&gt;
    &lt;title&gt;&lt;/title&gt;
&lt;/head&gt;
&lt;body&gt;
    &lt;script src="http://code.jquery.com/jquery-1.7.min.js" type="text/javascript"&gt;&lt;/script&gt;
  &lt;script src="Scripts/jquery.signalR-1.0.0-alpha1.min.js" type="text/javascript"&gt;&lt;/script&gt;
  &lt;script type="text/javascript"&gt;
      $(function () {
          var connection = $.connection('http://localhost:50865/echo');

          connection.received(function (data) {
              $('#messages').append('&lt;li&gt;' + data + '&lt;/li&gt;');
          });

          connection.start();

          $("#broadcast").click(function () {
              connection.send($('#msg').val());
          });
      });
  &lt;/script&gt;

  &lt;input type="text" id="msg" /&gt;
  &lt;input type="button" id="broadcast" value="broadcast" /&gt;

  &lt;ul id="messages"&gt;
  &lt;/ul&gt;
&lt;/body&gt;
&lt;/html&gt;
```
Make sure you reference the right jquery.signalr file and that you change the url to the signalr server. You know, that url I told you to remember earlier.

Now you will get a very boring page like this.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/SignalR/SignalR4.png?mtime=1352543346"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/SignalR/SignalR4.png?mtime=1352543346" width="256" height="67" /></a>
</div>

But let&#8217;s go back to our console app and start typing things in there, and see the magic happen.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/SignalR/SignalR5.png?mtime=1352543488"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/SignalR/SignalR5.png?mtime=1352543488" width="649" height="267" /></a>
</div>

And that&#8217;s not all you can also do this in the webpage.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/SignalR/SignalR6.png?mtime=1352543835"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/SignalR/SignalR6.png?mtime=1352543835" width="583" height="232" /></a>
</div>

Simples.

## Conclusion

SignalR is very easy to set up although the doc was wrong in some places. But nothing I couldn&#8217;t figure out on my own.

 [1]: https://github.com/SignalR/SignalR
 [2]: https://github.com/SignalR/SignalR/wiki/QuickStart-Persistent-Connections