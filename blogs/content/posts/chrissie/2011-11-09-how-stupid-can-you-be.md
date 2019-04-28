---
title: 'How stupid can you be: me and threads'
author: Christiaan Baes (chrissie1)
type: post
date: 2011-11-09T07:06:00+00:00
ID: 1376
excerpt: So this week I discovered I did a really stupid thing and this seems to happen on a regular basis to. It usually involves threads and the UI. Let me try to explain how stupid I was.
url: /index.php/desktopdev/mstech/how-stupid-can-you-be/
views:
  - 3269
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - thread
  - ui
  - winforms

---
## Introduction

So this week I discovered I did a really stupid thing and this seems to happen on a regular basis to. It usually involves threads and the UI. Let me try to explain how stupid I was.

## The beginning bit

Every story has a beginning and an end and you usually start of in the beginning without threads.

Let&#8217;s say I have 2 timer processes I want to run and I want to show the output of those processes on a form. But one of those processes takes a while to process, but not always, just sometimes.

Something like this.

```vbnet
Imports System.Threading

Public Class Form2

    Private WithEvents _t1 As New System.Windows.Forms.Timer
    Private WithEvents _t2 As New System.Windows.Forms.Timer

    Private Sub Form2_Load(sender As System.Object, e As System.EventArgs) Handles MyBase.Load
        _t1.Interval = 1000
        _t2.Interval = 2000
        _t1.Start()
        _t2.Start()
    End Sub

    Dim _random As New System.Random(CType(System.DateTime.Now.Ticks Mod System.Int32.MaxValue, Integer))

    Private Sub T1Tick(sender As Object, e As EventArgs) Handles _t1.Tick
        Dim rnd = _random.Next(0, 5) * 100
        Thread.Sleep(rnd)
        TextBox1.Text = DateTime.UtcNow.ToString()
    End Sub

    Private Sub T2Tick(sender As Object, e As EventArgs) Handles _t2.Tick
        TextBox2.Text = DateTime.UtcNow.ToString()
    End Sub

End Class
```
Of course the UI will be completely unresponsive when we run this and will only show something every so often. The solution of course is to use threads. And in particular the Threading.Timer.

## The middle bit

```vbnet
Imports System.Threading

Public Class Form2

    Private _t1 As Timer
    Private _t2 As Timer

    Private Sub Form1_Load(sender As System.Object, e As System.EventArgs) Handles MyBase.Load
        _t1 = New Timer(AddressOf T1Tick, Nothing, 0, 1000)
        _t2 = New Timer(AddressOf T2Tick, Nothing, 0, 200)
    End Sub

    Private Sub T2Tick(sender As Object)
        TextBox2.Text = DateTime.UtcNow.ToString()
    End Sub

    Dim _random As New System.Random(CType(System.DateTime.Now.Ticks Mod System.Int32.MaxValue, Integer))

    Private Sub T1Tick(sender As Object)
        Dim rnd = _random.Next(0, 5) * 100
        Thread.Sleep(rnd)
        TextBox1.Text = DateTime.UtcNow.ToString()
    End Sub

End Class

```
Of course the above won&#8217;t run. Why not? Because of the dreaded CrossThreadMessagingException.And we all know how to solve that very easily.

```vbnet
Imports System.Threading

Public Class Form2

    Private _t1 As Timer
    Private _t2 As Timer

    Private Sub Form1_Load(sender As System.Object, e As System.EventArgs) Handles MyBase.Load
        _t1 = New Timer(AddressOf T1Tick, Nothing, 0, 1000)
        _t2 = New Timer(AddressOf T2Tick, Nothing, 0, 200)
    End Sub

    Private Delegate Sub TickDelegate(sender As Object)

    Private Sub T2Tick(sender As Object)
        If InvokeRequired Then
            Invoke(New TickDelegate(AddressOf T2Tick), sender)
        Else
            TextBox2.Text = DateTime.UtcNow.ToString()
        End If
    End Sub

    Dim _random As New System.Random(CType(System.DateTime.Now.Ticks Mod System.Int32.MaxValue, Integer))

    Private Sub T1Tick(sender As Object)
        If InvokeRequired Then
            Invoke(New TickDelegate(AddressOf T1Tick), sender)
        Else
            Dim rnd = _random.Next(0, 5) * 100
            Thread.Sleep(rnd)
            TextBox1.Text = DateTime.UtcNow.ToString()
        End If
    End Sub

End Class

```
The above will kind of work. You will see the timers tick in a more smooth manner. But you will also note that sometimes it doesn&#8217;t run as smoothly as you would think. And that&#8217;s because we are doing it wrong. We are in fact just faking it. It just looks like we are using 3 threads (2 timers and the UI thread) but we are merging everything on the UI thread, so the UI thread is still blocking everything.

This is a very stupid mistake to make, and I admit I did that. 

## The end bit

In the end all you have to is to make sure that you keep the processes that merge with the UI thread as short as possible. The merging with the UI thread is the bit where you say invoke. That is the part where your other thread gets merged into the UI thread and they are more or less one and all your code is executed (and blocking) on that thread. So a simple change to the following code is all you need.

```vbnet
Imports System.Threading

Public Class Form1

    Private _t1 As Timer
    Private _t2 As Timer

    Private Sub Form1_Load(sender As System.Object, e As System.EventArgs) Handles MyBase.Load
        _t1 = New Timer(AddressOf T1Tick, Nothing, 5000, 1000)
        _t2 = New Timer(AddressOf T2Tick, Nothing, 0, 500)
    End Sub

    Private Delegate Sub TickDelegate(sender As Object)

    Private Sub T2Tick(sender As Object)
        If InvokeRequired Then
            Invoke(New TickDelegate(AddressOf T2Tick), sender)
        Else
            TextBox2.Text = DateTime.UtcNow.ToString()
        End If
    End Sub

    Dim _random As New System.Random(CType(System.DateTime.Now.Ticks Mod System.Int32.MaxValue, Integer))

    Private Sub T1Tick(sender As Object)
        Dim ti = DateTime.UtcNow.ToString()
        Dim rnd = _random.Next(0, 5) * 100
        Thread.Sleep(rnd)
        c_OnTimeCHanged(ti)
    End Sub

    Private Delegate Sub cDelegate(sender As String)

    Private Sub c_OnTimeCHanged(T As String)
        If InvokeRequired Then
            Invoke(New cDelegate(AddressOf c_OnTimeCHanged), T)
        Else
            TextBox1.Text = T
        End If
    End Sub

End Class
```
## Conclusion

Yep, sometimes you have to think before you do.