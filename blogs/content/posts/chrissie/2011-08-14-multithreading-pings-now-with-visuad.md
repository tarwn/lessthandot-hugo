---
title: Multithreading pings now with Visual studio Async CTP SP1 refresh
author: Christiaan Baes (chrissie1)
type: post
date: 2011-08-14T14:20:00+00:00
ID: 1300
excerpt: A while back I did a post about how to do pings in a multithreaded way and show them in a grid. I will now try to use the new Async way of doing things.
url: /index.php/desktopdev/mstech/multithreading-pings-now-with-visuad/
views:
  - 9020
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - async
  - vb.net

---
## Introduction

A while back I did a post about how to do pings in a [multithreaded way][1] and show them in a grid. I will now try to use [the new Async way of doing things][2]. 

## Multithreaded

This is the code I used for the multithreaded part.

```vbnet
Imports System.Threading
Imports System.Threading.Tasks
Imports System.Net.NetworkInformation

Partial Public Class Form1

    Private fThread As Thread
    
    Private Sub Form1_Load(ByVal sender As Object, ByVal e As EventArgs) Handles Me.Load
        fgrid.Columns.Add("Ip", "Ip")
        fgrid.Columns.Add("Ping", "Ping")
        fgrid.Columns(0).Width = 100
        fgrid.Columns(1).AutoSizeMode = DataGridViewAutoSizeColumnMode.Fill
        fgrid.Rows.Add()
        fgrid.Rows(0).Cells(0).Value = "google.com"
        fgrid.Rows.Add()
        fgrid.Rows(1).Cells(0).Value = "lessthandot.com"
        fgrid.Rows.Add()
        fgrid.Rows(2).Cells(0).Value = "bing.com"
        fgrid.Rows.Add()
        fgrid.Rows(3).Cells(0).Value = "yahoo.com"
        fgrid.Rows.Add()
        fgrid.Rows(4).Cells(0).Value = "127.0.0.1"
    End Sub

    Private Sub ThreadProc()
        Parallel.For(0, fgrid.RowCount, Sub(b)
                                            fgrid.Rows(b).Cells(1).Value = "Pinging " & fgrid.Rows(b).Cells(0).Value.ToString
                                            fgrid.Rows(b).Cells(1).Style.BackColor = Drawing.Color.White
                                            CheckOnline(b)
                                        End Sub)
    End Sub

     Private Sub CheckOnline(ByVal rowindex As Integer)
        Dim _ping As New Ping
        Dim _success As Boolean = False
        Try
            Dim _pingreply = _ping.Send(fgrid.Rows(rowindex).Cells(0).Value.ToString, 2000)
            If _pingreply.Status = IPStatus.Success Then
                _success = True
            End If
        Catch ex As Exception
        End Try
        SetOnline(rowindex, _success)
    End Sub

    Private Sub SetOnline(ByVal rowindex As Integer, ByVal success As Boolean)
        If Not success Then
            fgrid.Rows(rowindex).Cells(1).Value = "Offline"
            fgrid.Rows(rowindex).Cells(1).Style.BackColor = Drawing.Color.Red
        Else
            fgrid.Rows(rowindex).Cells(1).Value = "Online"
            fgrid.Rows(rowindex).Cells(1).Style.BackColor = Drawing.Color.Green
        End If
    End Sub

    Private Sub PingToolStripMenuItem_Click(ByVal sender As System.Object, ByVal e As System.EventArgs) Handles PingToolStripMenuItem.Click
        fThread = New Thread(New ThreadStart(AddressOf ))
        fThread.IsBackground = True
        fThread.Start()
    End Sub

End Class
```
With this as the result.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/ping/ping4.png?mtime=1313322948"><img alt="" src="/wp-content/uploads/users/chrissie1/ping/ping4.png?mtime=1313322948" width="391" height="280" /></a>
</div>

## Async

For the Aysnc version I first downloaded the Async CTP sp1 refresh and when trying to install it I noticed that I needed VS 2010 SP1 so I installed that too.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/ping/async1.png?mtime=1313323227"><img alt="" src="/wp-content/uploads/users/chrissie1/ping/async1.png?mtime=1313323227" width="576" height="513" /></a>
</div>

Both take quit a while to complete. But an hour later I was ready to begin.

Now you will have to reference the AsyncCtpLibrary.dll which you will find under my documents in the samples forlder of the Microsoft Visual Studio Async CTP folder that the installer put there.

I did not find it in nuget yet.

And the first thing to note is that you will have to look for extension methods and that these extension methods might be hidden by VB.Net as being to complicated for simple users like you and me.

But I found them under the all tab anyway.

I made the same form as above and just added this code in the eventhandler for the pingmenu above.

```vbnet
Private Sub PingToolStripMenuItem_Click(ByVal sender As System.Object, ByVal e As System.EventArgs) Handles PingToolStripMenuItem.Click
        For i As Integer = 0 To 4
            fgrid.Rows(i).Cells(1).Value = "Pinging " & fgrid.Rows(i).Cells(0).Value.ToString
            fgrid.Rows(i).Cells(1).Style.BackColor = Drawing.Color.White
            CheckOnline(i)
        Next
    End Sub```
As you can see no threading or Async code in here yet.

All the good bits are here.

```vbnet
Private Async Sub CheckOnline(ByVal rowindex As Integer)
        Dim _ping As New Ping
        Dim _succes As Boolean = False
        Try
            Dim _pingreply = Await _ping.SendTaskAsync(fgrid.Rows(rowindex).Cells(0).Value.ToString, 2000)
            If _pingreply.Status = IPStatus.Success Then
                _succes = True
            End If
        Catch ex As Exception
        End Try
        SetOnline(rowindex, _succes)
    End Sub

    Private Sub SetOnline(ByVal rowindex As Integer, online As Boolean)
        If Not online Then
            fgrid.Rows(rowindex).Cells(1).Value = "Offline"
            fgrid.Rows(rowindex).Cells(1).Style.BackColor = Drawing.Color.Red
        Else
            fgrid.Rows(rowindex).Cells(1).Value = "Online"
            fgrid.Rows(rowindex).Cells(1).Style.BackColor = Drawing.Color.Green
        End If
    End Sub```
As you can see I made the checkonline method Async and I added <code class="codespan">Dim _pingreply = Await _ping.SendTaskAsync(fgrid.Rows(rowindex).Cells(0).Value.ToString, 2000)</code>. You can see that I used the SendTaskAsync method which is an extension method which just magically appeared and I use the Await keyword. You need all three of the above to get it to work. If you miss the Await keyword it will just run SYnc and that won&#8217;t give you the result you need. 

I think it is a lot less code then it used to be. 

And here is the complete thing like above.

```vbnet
Imports System.Net.NetworkInformation

Public Class Form1
    Private Sub Form1_Load(ByVal sender As Object, ByVal e As EventArgs) Handles Me.Load
        fgrid.Columns.Add("Ip", "Ip")
        fgrid.Columns.Add("Ping", "Ping")
        fgrid.Columns(0).Width = 100
        fgrid.Columns(1).AutoSizeMode = DataGridViewAutoSizeColumnMode.Fill
        fgrid.Rows.Add()
        fgrid.Rows(0).Cells(0).Value = "google.com"
        fgrid.Rows.Add()
        fgrid.Rows(1).Cells(0).Value = "lessthandot.com"
        fgrid.Rows.Add()
        fgrid.Rows(2).Cells(0).Value = "bing.com"
        fgrid.Rows.Add()
        fgrid.Rows(3).Cells(0).Value = "yahoo.com"
        fgrid.Rows.Add()
        fgrid.Rows(4).Cells(0).Value = "127.0.0.1"
    End Sub

    Private Sub PingToolStripMenuItem_Click(ByVal sender As System.Object, ByVal e As System.EventArgs) Handles PingToolStripMenuItem.Click
        For i As Integer = 0 To 4
            fgrid.Rows(i).Cells(1).Value = "Pinging " & fgrid.Rows(i).Cells(0).Value.ToString
            fgrid.Rows(i).Cells(1).Style.BackColor = Drawing.Color.White
            CheckOnline(i)
        Next
    End Sub

    Private Async Sub CheckOnline(ByVal rowindex As Integer)
        Dim _ping As New Ping
        Dim _succes As Boolean = False
        Try
            Dim _pingreply = Await _ping.SendTaskAsync(fgrid.Rows(rowindex).Cells(0).Value.ToString, 2000)
            If _pingreply.Status = IPStatus.Success Then
                _succes = True
            End If
        Catch ex As Exception
        End Try
        SetOnline(rowindex, _succes)
    End Sub

    Private Sub SetOnline(ByVal rowindex As Integer, online As Boolean)
        If Not online Then
            fgrid.Rows(rowindex).Cells(1).Value = "Offline"
            fgrid.Rows(rowindex).Cells(1).Style.BackColor = Drawing.Color.Red
        Else
            fgrid.Rows(rowindex).Cells(1).Value = "Online"
            fgrid.Rows(rowindex).Cells(1).Style.BackColor = Drawing.Color.Green
        End If
    End Sub

End Class
```
## Conclusion

Async takes a bit of getting used to and you still have to tell it when and what you want to be async but it seems a bit less code then it used to take for the same thing.

 [1]: /index.php/DesktopDev/MSTech/multithreading-pings-and-showing-them
 [2]: /index.php/DesktopDev/MSTech/visual-studio-async-ctp-videos