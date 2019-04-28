---
title: Multithreading pings and showing them in a grid in VB.Net
author: Christiaan Baes (chrissie1)
type: post
date: 2011-04-14T11:54:00+00:00
ID: 1114
excerpt: |
  Today I wanted a way to see which of my cameras were online at a certain point in time. Pinging them is a great way of doing that, it at least sayss they are there and the first two layers of the OSI model are working (if I remember correctly). 
  
  Doin&hellip;
url: /index.php/desktopdev/mstech/multithreading-pings-and-showing-them/
views:
  - 19140
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - grid
  - multithreading
  - ping
  - vb.net

---
Today I wanted a way to see which of my cameras were online at a certain point in time. Pinging them is a great way of doing that, it at least says they are there and the first two layers of the OSI model are working (if I remember correctly). 

Doing a ping to another computer in VB.Net is easy. 

```vbnet
Dim _ping As New Ping
Dim _pingreply = _ping.Send(IpAddress, 2000)
If _pingreply.Status = IPStatus.Success Then
Like this.```
The first parameter of Ping.Send is the IpAddress as string and the second is the timeout in this case 2 seconds.

I want to ping 5 cameras at a time to worst case this will take 10 seconds. 10 seconds is a long time to wait for a user who&#8217;s attention span is 3 seconds. So we need a way to do this asynchronously. 

So I create a grid with 2 columns and 5 rows. First column has the Ipaddresses and the second will show the result.

Here is how.

```vbnet
Private Sub Form1_Load(ByVal sender As Object, ByVal e As EventArgs) Handles Me.Load
  fGrid.Columns.Add("Ip", "Ip")
  fGrid.Columns.Add("Ping", "Ping")
  fGrid.Columns(0).Width = 100
  fGrid.Columns(1).AutoSizeMode = DataGridViewAutoSizeColumnMode.Fill
  For i As Integer = 0 To 4
    Dim myRowIndex = fGrid.Rows.Add()
      fGrid.Rows(myRowIndex).Cells(0).Value = "10.216.110." & (11 + myRowIndex).ToString
  Next
End Sub```
Looks like this.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/ping/ping1.png?mtime=1302788168"><img alt="" src="/wp-content/uploads/users/chrissie1/ping/ping1.png?mtime=1302788168" width="425" height="410" /></a>
</div>

So now that we have that we can do the pings in another thread. So I made a button to spawn this thread and begin the process.

```vbnet
Private fThread = New Thread(New ThreadStart(AddressOf ThreadProc))
fThread.IsBackground = True
fThread.Start()
		```
and this will use the following things.

```vbnet
Private Sub ThreadProc()
		Parallel.For(0, 5, Sub(b)
							   CheckOnline(b)
						   End Sub)
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
	End Sub```
Yep I used a parallel foreach that will spawn 5 more threads to do the pings in parallel for me.

But I also needed a way to tell my users I was doing something and a way to tell them it was all finished and the result.

I did that with a second thread that check the result in a list and sees if they are done, making sure I synchronize the lists add method because that is not threadsafe. 

```vbnet
Imports System.Threading
Imports System.Threading.Tasks
Imports System.Net.NetworkInformation

Partial Public Class Form1

	Private fThread As Thread
	Private fThread2 As Thread

	Public Delegate Sub AddRowDelegate(ByVal column As Integer)
	Delegate Sub CheckOnlineDelegate(ByVal rowindex As Integer)
	Delegate Sub SetOnlineDelegate(ByVal rowindex As Integer)

	Private Sub Form1_Load(ByVal sender As Object, ByVal e As EventArgs) Handles Me.Load
		fGrid.Columns.Add("Ip", "Ip")
		fGrid.Columns.Add("Ping", "Ping")
		fGrid.Columns(0).Width = 100
		fGrid.Columns(1).AutoSizeMode = DataGridViewAutoSizeColumnMode.Fill
		For i As Integer = 0 To 4
			Dim myRowIndex = fGrid.Rows.Add()
			fGrid.Rows(myRowIndex).Cells(0).Value = "10.216.110." & (11 + myRowIndex).ToString
		Next
	End Sub

	Private Sub ThreadProc()
		Parallel.For(0, 5, Sub(b)
							   Do While Not done.Contains(b)
								   fGrid.Invoke(New AddRowDelegate(AddressOf AddRow), New Object() {b})
								   Thread.Sleep(300)
							   Loop
							   fGrid.Invoke(New SetOnlineDelegate(AddressOf SetOnline), New Object() {b})
						   End Sub)
	End Sub

	Private Sub ThreadProc2()
		Parallel.For(0, 5, Sub(b)
							   CheckOnline(b)
						   End Sub)
	End Sub

	
	Private Sub AddRow(ByVal rowindex As Integer)
		If fGrid.Rows(rowindex).Cells(1).Value Is Nothing OrElse fGrid.Rows(rowindex).Cells(1).Value.ToString.Contains(".....") OrElse Not fGrid.Rows(rowindex).Cells(1).Value.ToString.Contains("Pinging") Then
			fGrid.Rows(rowindex).Cells(1).Value = "Pinging 10.216.110." & (11 + rowindex).ToString & " "
		Else
			fGrid.Rows(rowindex).Cells(1).Value = fGrid.Rows(rowindex).Cells(1).Value.ToString & "."
		End If
		fGrid.Rows(rowindex).Cells(1).Style.BackColor = Drawing.Color.White
	End Sub

	Private success As New List(Of Integer)
	Private done As New List(Of Integer)

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
	
End Class
```
As you can see I invoke the SetOnline and AddRow methods via the grid so that it can be handled in a thread safe way. I also use delegates for that. 

While running it you will see that if one is online it will turn green immediately or very fast.

Look also that the Parallel For second parameter is Exclusive To so it does not translate into For i as integer = 0 to 5 but it translates into For i as integer = 0 to 4. 

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/ping/ping2.png?mtime=1302788818"><img alt="" src="/wp-content/uploads/users/chrissie1/ping/ping2.png?mtime=1302788818" width="425" height="410" /></a>
</div>

And that you have to wait for about 2 (timeout we used) seconds for all the others to turn red.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/ping/ping3.png?mtime=1302788825"><img alt="" src="/wp-content/uploads/users/chrissie1/ping/ping3.png?mtime=1302788825" width="425" height="410" /></a>
</div>

Writing thread safe code is tricky but I think I covered all the bases to make this as thread safe as possible without getting race conditions.

Any questions?