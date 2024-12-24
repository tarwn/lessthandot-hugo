---
title: Seeing the sql Simple.Data generates
author: Christiaan Baes (chrissie1)
type: post
date: 2012-04-29T08:18:00+00:00
ID: 1609
excerpt: |
  I'm trying out Simple.Data and one of the things you always want to see from an ORM is what SQL it generates for you. Not that we don't trust the author but still.
  In the case of Nhibernate is simple, you just use NHProf. For Simple.Data there is no such thing. But there is an easy way to see it by intercepting the Trace information Mark was so gentle to provide for us.
url: /index.php/desktopdev/mstech/seeing-the-sql-simple-data/
views:
  - 5409
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - simple.data
  - trace
  - tracelisteners

---
## Introduction

I&#8217;m [trying out Simple.Data][1] and one of the things you always want to see from an ORM is what SQL it generates for you. Not that we don&#8217;t trust the author but still.
  
In the case of Nhibernate is simple, you just use NHProf. For Simple.Data there is no such thing. But there is an easy way to see it by intercepting the Trace information Mark was so gentle to provide for us.

## Tracing

First of all, this is the test code I used.

```vbnet
Option Strict Off

Imports System.Data.SqlServerCe
Imports System.IO

Module Module1

    Sub Main()
        CreateDatabase()
        CreateTablePerson()
        Dim db = Simple.Data.Database.OpenConnection("DataSource=""test.sdf""; Password=""mypassword""")
        db.Person.Insert(New With {.LastName = "lastname1", .FirstName = "firstname1"})
        db.Person.Insert(New With {.LastName = "lastname1", .FirstName = "firstname2"})
        Dim result = db.Persons.FindAllByLastName("lastname1")
        For Each person In result
            Console.WriteLine(person.LastName & " " & person.FirstName)
        Next
        Dim count = db.Persons.FindAllByLastName("lastname1").Count
        Console.WriteLine("Count: " & count)
        Console.ReadLine()
    End Sub

    Private Function CreateDatabase() As SqlCeEngine
        If File.Exists("test.sdf") Then File.Delete("test.sdf")
        Dim connectionString = "DataSource=""test.sdf""; Password=""mypassword"""
        Dim en = New SqlCeEngine(connectionString)
        en.CreateDatabase()
        Return en
    End Function

    Private Sub CreateTablePerson()
        Using cn = New SqlCeConnection("DataSource=""test.sdf""; Password=""mypassword""")
            If cn.State = ConnectionState.Closed Then
                cn.Open()
            End If
            Dim sql = "create table Person (LastName nvarchar (40) not null, FirstName nvarchar (40))"
            Using cmd = New SqlCeCommand(sql, cn)
                cmd.ExecuteNonQuery()
            End Using
        End Using
    End Sub
End Module
```
So where does that trace information end up. 

Normally it should end up in the Output window under Debug.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/simpledata/simpledata2.png?mtime=1335694334"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/simpledata/simpledata2.png?mtime=1335694334" width="711" height="464" /></a>
</div>

But this depends on a little setting in Tools->Options->Debugging

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/simpledata/simpledata1.png?mtime=1335694325"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/simpledata/simpledata1.png?mtime=1335694325" width="757" height="440" /></a>
</div>

If this options is checked than the output appears in the immediate window.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/simpledata/simpledata3.png?mtime=1335694344"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/simpledata/simpledata3.png?mtime=1335694344" width="711" height="466" /></a>
</div>

But you can also add tracelisteners to output the traceoutput to somewhere else.

This will just add them to your console output.

```vbnet
Trace.Listeners.Add(New TextWriterTraceListener(Console.Out))```
Which will output this.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/simpledata/simpledata4.png?mtime=1335694355"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/simpledata/simpledata4.png?mtime=1335694355" width="677" height="518" /></a>
</div>

You can read all about Tracelisteners on [MSDN][2].

 [1]: /index.php/DesktopDev/MSTech/simple-data-and-vb-net
 [2]: http://msdn.microsoft.com/en-us/library/4y5y10s7.aspx