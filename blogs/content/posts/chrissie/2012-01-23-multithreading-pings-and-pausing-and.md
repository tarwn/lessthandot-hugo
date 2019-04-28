---
title: Multithreading pings and pausing and resuming and grouping them.
author: Christiaan Baes (chrissie1)
type: post
date: 2012-01-23T11:09:00+00:00
ID: 1505
excerpt: Where I respond to a question from a commenter and show that I am a good guy, despite what you all think.
url: /index.php/desktopdev/mstech/multithreading-pings-and-pausing-and/
views:
  - 6313
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - threading
  - vb.net

---
In my post [Multithreading pings and showing them in a grid in VB.Net][1] I got a question from Alex. 

> Hi,
  
> What if I want to check 100 ip&#8217;s 5 at a time? What do I need to change in the code to accomplish this. I&#8217;ve been working on it for several hours with no luck.

And Alex was pushing his luck by coming back and asking this too.

> Will it also be possible to pause/resume checking as this would be handy?

But I aim to please so I did what Alex wanted.

The app pings 6 Ip-addresses in groups of 2 in an Async matter. 

You can pause the pings, and it will stop/pause as soon as it is finished with the 2 it was pinging. 

I just added 2 extra menu items.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/ping/GroupPing1.png?mtime=1327323869"><img alt="" src="/wp-content/uploads/users/chrissie1/ping/GroupPing1.png?mtime=1327323869" width="649" height="610" /></a>
</div>

It just starts when you click Ping.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/ping/GroupPing2.png?mtime=1327323878"><img alt="" src="/wp-content/uploads/users/chrissie1/ping/GroupPing2.png?mtime=1327323878" width="649" height="610" /></a>
</div>

And it pauses when you click Pause.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/ping/GroupPing3.png?mtime=1327323887"><img alt="" src="/wp-content/uploads/users/chrissie1/ping/GroupPing3.png?mtime=1327323887" width="649" height="610" /></a>
</div>

And here is the enormous amount of code.

```vbnet
Imports System.Threading
Imports System.Threading.Tasks
Imports System.Net.NetworkInformation

Partial Public Class Form1

    Private fThread As Thread
    Private fThread2 As Thread
    Private waitHandle As ManualResetEvent = New ManualResetEvent(True)

    Public Delegate Sub AddRowDelegate(ByVal column As Integer)
    Delegate Sub CheckOnlineDelegate(ByVal rowindex As Integer)
    Delegate Sub SetOnlineDelegate(ByVal rowindex As Integer)

    Private success As New List(Of Integer)
    Private done As New List(Of Integer)

    Private Sub Form1_Load(ByVal sender As Object, ByVal e As EventArgs) Handles Me.Load
        fGrid.Columns.Add("Ip", "Ip")
        fGrid.Columns.Add("Ping", "Ping")
        fGrid.Columns(0).Width = 100
        fGrid.Columns(1).AutoSizeMode = DataGridViewAutoSizeColumnMode.Fill
        For i As Integer = 0 To 5
            Dim myRowIndex = fGrid.Rows.Add()
            fGrid.Rows(myRowIndex).Cells(0).Value = "10.216.110." & (11 + myRowIndex).ToString
        Next
    End Sub

    Private Sub ThreadProc()
        For i As Integer = 0 To 2
            waitHandle.WaitOne()
            Parallel.For(0 + (i * 2), 2 + (i * 2), Sub(b)
                                                       Do While Not done.Contains(b)
                                                           fGrid.Invoke(New AddRowDelegate(AddressOf AddRow), New Object() {b})
                                                           Thread.Sleep(300)
                                                       Loop
                                                       fGrid.Invoke(New SetOnlineDelegate(AddressOf SetOnline), New Object() {b})
                                                   End Sub)
        Next
    End Sub

    Private Sub ThreadProc2()
        For i As Integer = 0 To 2
            waitHandle.WaitOne()
            Parallel.For(0 + (i * 2), 2 + (i * 2), Sub(b)
                                                       CheckOnline(b)
                                                   End Sub)
        Next
    End Sub


    Private Sub AddRow(ByVal rowindex As Integer)
        If fGrid.Rows(rowindex).Cells(1).Value Is Nothing OrElse fGrid.Rows(rowindex).Cells(1).Value.ToString.Contains(".....") OrElse Not fGrid.Rows(rowindex).Cells(1).Value.ToString.Contains("Pinging") Then
            fGrid.Rows(rowindex).Cells(1).Value = "Pinging 10.216.110." & (11 + rowindex).ToString & " "
        Else
            fGrid.Rows(rowindex).Cells(1).Value = fGrid.Rows(rowindex).Cells(1).Value.ToString & "."
        End If
        fGrid.Rows(rowindex).Cells(1).Style.BackColor = Drawing.Color.White
    End Sub

    Private Sub CheckOnline(ByVal rowindex As Integer)
        Dim _ping As New Ping
        Try
            Dim _pingreply = _ping.Send("10.216.110." & (11 + rowindex).ToString, 2000)
            If _pingreply.Status = IPStatus.Success Then
                SyncLock success
                    success.Add(rowindex)
                End SyncLock
            End If
        Catch ex As Exception
        End Try
        SyncLock done
            done.Add(rowindex)
        End SyncLock
    End Sub

    Private Sub SetOnline(ByVal rowindex As Integer)
        If Not success.Contains(rowindex) Then
            fGrid.Rows(rowindex).Cells(1).Value = "Offline"
            fGrid.Rows(rowindex).Cells(1).Style.BackColor = Drawing.Color.Red
        Else
            fGrid.Rows(rowindex).Cells(1).Value = "Online"
            fGrid.Rows(rowindex).Cells(1).Style.BackColor = Drawing.Color.Green
        End If
    End Sub

    Private Sub PingToolStripMenuItem_Click(ByVal sender As System.Object, ByVal e As System.EventArgs) Handles PingToolStripMenuItem.Click
        done = New List(Of Integer)
        success = New List(Of Integer)
        fThread = New Thread(New ThreadStart(AddressOf ThreadProc))
        fThread.IsBackground = True
        fThread.Start()
        fThread2 = New Thread(New ThreadStart(AddressOf ThreadProc2))
        fThread2.IsBackground = True
        fThread2.Start()
    End Sub

    Private Sub StopToolStripMenuItem_Click(sender As System.Object, e As System.EventArgs) Handles StopToolStripMenuItem.Click
        waitHandle.Reset()
    End Sub

    Private Sub ResumeToolStripMenuItem_Click(sender As System.Object, e As System.EventArgs) Handles ResumeToolStripMenuItem.Click
        waitHandle.Set()
    End Sub
End Class```
Not pretty, but functional. I used the ManualResetEvent to Pause and resume the pings and I just added an extra for next to the Threadprocs to make the grouping happen.

All quite simple if you know how.

 [1]: /index.php/DesktopDev/MSTech/multithreading-pings-and-showing-them