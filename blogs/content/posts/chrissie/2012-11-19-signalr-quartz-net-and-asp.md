---
title: SignalR, Quartz.Net and ASP.Net
author: Christiaan Baes (chrissie1)
type: post
date: 2012-11-19T05:38:00+00:00
ID: 1793
excerpt: Using Quartz.net and SignalR to send messages to the clients on timed intervals.
url: /index.php/webdev/serverprogramming/signalr-quartz-net-and-asp/
views:
  - 4965
categories:
  - ASP.NET
  - Server Programming
tags:
  - quartz.net
  - signalr

---
## Introduction

I needed a way to let my users know when something new arrived. For this I need to check an external system. The only way to do this is to pole the existing system every so often. I could easily do this on the clients, but this would create a lot of traffic if you had lots of clients. I decided have my server do the polling and then let the clients know when something new has arrived. 

The best way I have found to this was to use [Quartz.Net][1] and SignalR.

## The server

To start you can make an empty ASP.Net project.

Normally you would make a service to something like this. But I kinda need my webserver for the SignalR part. But Quartz.Net can deal with this. 

First thing to do is to make our Quartz.Net Job.

```vbnet
Option Strict Off

Imports Microsoft.AspNet.SignalR
Imports Quartz
Imports Dapper

Namespace Server

    Public Class NewCases
        Implements IJob
        
        Private ReadOnly _context As Object

        Public Sub New()
            _context = GlobalHost.ConnectionManager.GetHubContext(Of Server.BeCare)()
        End Sub

        Public Sub Execute(context As IJobExecutionContext) Implements IJob.Execute
            Try
                Using con = New SqlConnection("connectionstring")
                    dim newCases = con.Query(Of String)("select * from newcases where status = 'new'").ToList
                    If newCases.Count &gt; 0 Then
                      _context.Clients.All.newCases(newCases)
                    End If 
                End Using
            Catch ex As Exception
                _context.Clients.All.newCases(New List(Of String) From {ex.Message})
            End Try
        End Sub

    End Class
End Namespace```
I&#8217;m using dapper to get my data and I&#8217;m using the globalhost to get the context for SignalR so I can send a message to all my clients whenever there are new cases available.

Now I just have to schedule this.

I can do the configuration in code in the Global.asax

```vbnet
Private Shared _scheduler As IScheduler

    Sub Application_Start(ByVal sender As Object, ByVal e As EventArgs)
        RouteTable.Routes.MapHubs()

        Dim factory = New StdSchedulerFactory()
        _scheduler = factory.GetScheduler()
        Dim startTime = DateBuilder.NextGivenSecondDate(Nothing, 10)
        Dim job = JobBuilder.Create(Of NewCases)().WithIdentity("job6", "group1").Build()

        Dim trigger As ISimpleTrigger = TriggerBuilder.Create().WithIdentity("trigger6", "group1").StartAt(startTime).WithSimpleSchedule(Function(x) x.WithIntervalInSeconds(5).RepeatForever()).Build()

        _scheduler.ScheduleJob(job, trigger)
        _scheduler.Start()
    End Sub```
SO I create a scheduler, then a job and a trigger and add those to the scheduler after which I can Start the scheduler. I made a trigger with an interval of 5 seconds here. And I made it start 10 seconds after our application starts.

I also made a Hub so that our clients have something to connect to.

```vbnet
Option Strict Off

Imports Microsoft.AspNet.SignalR.Hubs
Imports System.Data.SqlClient
Imports Dapper

Namespace Server
    Public Class BeCare
        Inherits Hub

        Public Sub Refresh()
            Try
                Using con = New SqlConnection("connectionstring")
                    Dim newCases = con.Query(Of String)("select * from newcases where status = 'new'").ToList
                    If newCases.Count &gt; 0 Then
                        Clients.All.newCases(newCases)
                    End If
                End Using
            Catch ex As Exception
                Clients.All.newCases(New List(Of String) From {ex.Message})
            End Try
        End Sub
        
    End Class
End Namespace```


## The client

I made a basic client to test if this works.

```vbnet
Dim connection = New HubConnection("http://localhost:54155/")

        Dim becare= connection.CreateHubProxy("BeCare")

        becare.On(Of IList(Of String))("newCases", Sub(data)
                                                       If data IsNot Nothing Then
                                                           Console.WriteLine(DateTime.Now.ToString("dd/MM/yyyy HH:mm:ss") & " - Found " & data.Count & " new cases.")
                                                           For Each s In data
                                                               Console.WriteLine(s)
                                                           Next
                                                       Else
                                                           Console.WriteLine("No records found")
                                                       End If
                                                   End Sub)

        connection.Start().Wait()

        Dim line As String = Nothing
        Console.WriteLine("type exit + enter to exit.")
        While line &lt;&gt; "exit"
            line = Console.ReadLine()
        End While```
And that&#8217;s it.

 [1]: http://www.quartz-scheduler.net/