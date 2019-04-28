---
title: Topshelf and VB.Net
author: Christiaan Baes (chrissie1)
type: post
date: 2012-09-16T08:10:00+00:00
ID: 1723
excerpt: Trying out topshelf with VB.Net. Topshelf is one of the simplest ways to get started with winservice development.
url: /index.php/desktopdev/mstech/topshelf-and-vb-net/
views:
  - 3193
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - topshelf

---
## Introduction

So what is [TopShelf][1]

According to the site.

> One of the simplest ways to get started with winservice development

In other words it&#8217;s an easier way to make a windows service.

## Getting started

To get started you create a consoleapplication and add topshelf via nuget (this is 2012 after all). 

Then we create a class with a stop and start method to begin with.

```vbnet
Public Class ServiceClass

        Public Sub StartService()
            WriteToEventLog("Service started")
        End Sub
        Public Sub StopService()
            WriteToEventLog("Service stopped")
        End Sub

        Private Sub WriteToEventLog(ByVal Message As String)
            Dim el As New EventLog("Application")
            el.Source = "VSS"
            Try
                el.WriteEntry(Message, EventLogEntryType.Information)
            Catch ex As Exception
                Debug.WriteLine(ex.Message)
            End Try
        End Sub
    End Class```
This writes something to the eventlog using the VSS eventsource, because that was there and I was to lazy to create my own source.

I called my methods StartService and StopService because Stop is a reserved word in VB.

And then we have to configure our service.

```vbnet
Imports Topshelf

Module Module1

    Sub Main()
        HostFactory.Run(
            Sub(x)
                x.Service(Of ServiceClass)(
                    Sub(s)
                        s.ConstructUsing(Function(name) New ServiceClass())
                        s.WhenStarted(Sub(tc) tc.StartService())
                        s.WhenStopped(Sub(tc) tc.StopService())
                    End Sub)
                x.RunAsLocalSystem()
                x.SetDescription("Sample Topshelf Host With VB")
                x.SetDisplayName("Displayname of VBService")
                x.SetServiceName("VBService")
            End Sub)
    End Sub
End Module
```
If we now got to our eventlog there will be an entry for the VSS source there that has this in the body.

We can now run this.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/topshelf/topshelf2.png?mtime=1347789945"><img alt="" src="/wp-content/uploads/users/chrissie1/topshelf/topshelf2.png?mtime=1347789945" width="677" height="343" /></a>
</div>

In the evntlog we will now see this.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/topshelf/topshelf1.png?mtime=1347789859"><img alt="" src="/wp-content/uploads/users/chrissie1/topshelf/topshelf1.png?mtime=1347789859" width="724" height="397" /></a>
</div>

And if we exit with Ctrl+C we will see this.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/topshelf/topshelf3.png?mtime=1347790136"><img alt="" src="/wp-content/uploads/users/chrissie1/topshelf/topshelf3.png?mtime=1347790136" width="723" height="419" /></a>
</div>

## Conclusion

So it works and is fairly easy to setup and make it work.

 [1]: http://topshelf-project.com/