---
title: An extension method for getting rid of those cross thread UI calls in winforms
author: Christiaan Baes (chrissie1)
type: post
date: 2012-07-10T05:55:00+00:00
ID: 1669
excerpt: An extension method for writing cross thread safe code.
url: /index.php/desktopdev/mstech/an-extension-method-for-getting/
views:
  - 2144
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - cross thread
  - vb.net

---
## Introduction

I was just reading Will Green&#8217;s ([Hotgazpacho][1]) blogpost on using the [RX extensions with winforms][2]. And there he references [Asynchronous Control Updates In C#/.NET/WinForms][3] by Derrick Bailey. And I swear I never read about that trick (or more likely, I forgot. So I write about it here so I will not forget (yeah right).

Since we are still using threads and not the Async-Await keywords which will make these things go away we still have to write cross thread safe code in winforms. 

## bad code

Here is an example of code that will not work.

```vbnet
Imports System.Threading

Public Class Form1
    Private _counter As Integer

    Private Sub StartCounting()
        Dim timer1 As New Threading.Timer(New TimerCallback(AddressOf UpdateTextBox), Nothing, 1, 5000)
    End Sub

    Private Sub UpdateTextBox(ByVal obj As Object)
        _counter += 1
        TextBox1.Text = _counter.ToString()
    End Sub

    Private Sub Button1_Click(sender As System.Object, e As System.EventArgs) Handles Button1.Click
        StartCounting()
    End Sub
End Class
```
You will get this exception.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/crossthread/threading1.png?mtime=1341905293"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/crossthread/threading1.png?mtime=1341905293" width="965" height="137" /></a>
</div>

## Good ugly code

We can fix the above by adding a bit of magic.

```vbnet
Imports System.Threading

Public Class Form1
    Private _counter As Integer

    Private Sub StartCounting()
        Dim timer1 As New Threading.Timer(New TimerCallback(AddressOf UpdateTextBox), Nothing, 1, 5000)
    End Sub

    Private Sub UpdateTextBox(ByVal obj As Object)
        If Me.InvokeRequired Then
            Invoke(Sub(x) UpdateTextBox(obj), obj)
        Else
            _counter += 1
            TextBox1.Text = _counter.ToString()
        End If
        
    End Sub

    Private Sub Button1_Click(sender As System.Object, e As System.EventArgs) Handles Button1.Click
        StartCounting()
    End Sub
End Class
```
The problem here is that you will have to repeat that a lot. And you seem to forget how to do that every time you need it. So let&#8217;s move on to something a little simpler.

## The better code

```vbnet
Imports System.Threading
Imports System.Runtime.CompilerServices

Public Class Form1
    Private _counter As Integer

    Private Sub StartCounting()
        Dim timer1 As New Threading.Timer(New TimerCallback(AddressOf UpdateTextBox), Nothing, 1, 5000)
    End Sub

    Private Sub UpdateTextBox(ByVal obj As Object)
        TextBox1.DoCrossThreadCall(Sub(x)
                                       _counter += 1
                                       x.Text = _counter.ToString()
                                   End Sub)
    End Sub

    Private Sub Button1_Click(sender As System.Object, e As System.EventArgs) Handles Button1.Click
        StartCounting()
    End Sub
End Class

Public Module ControlExtensions

    &lt;Extension()&gt;
    Public Sub DoCrossThreadCall(Of TControl As Control)(ByVal control As TControl, ByVal controlAction As Action(Of TControl))
        If control.InvokeRequired Then
            control.Invoke(controlAction, control)
        Else
            controlAction(control)
        End If
    End Sub
End Module
```
I can now use that extension everywhere I need it and keep the other code cleaner. The extension method is called DoCrossThreadCall just in case you didn&#8217;t notice.

## Conclusion

This extension method makes your code a whole lot cleaner. But if you have a chance you should use the Async-Await keywords in the next version of .net since that will take care of that problem all by itself.

 [1]: http://hotgazpacho.org/
 [2]: http://hotgazpacho.org/2012/07/reactive-extensions-with-winforms/
 [3]: http://lostechies.com/derickbailey/2011/01/24/asynchronous-control-updates-in-c-net-winforms/