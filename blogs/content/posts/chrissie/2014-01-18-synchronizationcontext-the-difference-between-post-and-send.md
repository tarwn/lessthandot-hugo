---
title: SynchronizationContext the difference between Post and Send
author: Christiaan Baes (chrissie1)
type: post
date: 2014-01-18T10:52:54+00:00
ID: 2265
excerpt: Trying to show the difference between Send and Post on the SynchronizationContext object.
url: /index.php/desktopdev/mstech/vbnet/synchronizationcontext-the-difference-between-post-and-send/
featured_image: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2014/01/Synch1.png
views:
  - 7254
categories:
  - VB.NET
tags:
  - Post
  - Send
  - SynchronizationContext
  - vb.net

---
I&#8217;ve been using SynchronizationContext for quite a while now. To me it is the best way to synchronize with the main thread. If you do a lot of winforms development like I do than you will know that you need to do this a lot. Blocking the UI is a nono.

<span style="font-size: 14px; line-height: 1.5em;">SynchronizationContext has 2 methods that are of interest to us. Send and Post. According to the <a title="MSDN" href="http://msdn.microsoft.com/en-us/library/system.threading.synchronizationcontext(v=vs.110).aspx" target="_blank">MSDN </a>documentation. </span>

  * Post: When overridden in a derived class, dispatches an asynchronous message to a synchronization context.
  * Send: When overridden in a derived class, dispatches a synchronous message to a synchronization context.

Meaning that Post is Async and Send is Synch. In most cases you won&#8217;t notice much difference between the two. And in most cases I would just recommend Post. Since in most cases you are just using the Synchronizationcontext to show something on the screen. I will however show you when Send is the better choice. And that is when you need to read from a control and need that result for later processing. This is a very simplified case but it makes the point quite nicely.

Lets make a form like this.

&nbsp;

<div id="attachment_2266" style="width: 310px" class="wp-caption alignnone">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2014/01/Synch1.png"><img class="size-full wp-image-2266" alt="Empty form with 3 textboxes and a button" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2014/01/Synch1.png" width="300" height="300" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2014/01/Synch1.png 300w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2014/01/Synch1-150x150.png 150w" sizes="(max-width: 300px) 100vw, 300px" /></a>
  
  <p class="wp-caption-text">
    Empty form with 3 textboxes and a button
  </p>
</div>

And now add this code.

```vbnet
Imports System.Threading

Public Class Form1

    Private _synchro As SynchronizationContext

    Public Sub New()

        ' This call is required by the designer.
        InitializeComponent()

        ' Add any initialization after the InitializeComponent() call.
        _synchro = SynchronizationContext.Current
    End Sub

    Private Sub Button1_Click(sender As Object, e As EventArgs) Handles Button1.Click
        Task.Run(Sub()
                     DoMath(TextBox2, GetNumberSync)
                     DoMath(TextBox3, GetNumberASync)
                 End Sub)
    End Sub

    Private Function GetNumberSync() As Integer
        Dim returnvalue As Integer
        _synchro.Send(Sub()
                          returnvalue = Convert.ToInt32(TextBox1.Text)
                      End Sub, Nothing)
        Return returnvalue
    End Function

    Private Function GetNumberASync() As Integer
        Dim returnvalue As Integer
        _synchro.Post(Sub()
                          returnvalue = Convert.ToInt32(TextBox1.Text)
                      End Sub, Nothing)
        Return returnvalue
    End Function

    Private Sub DoMath(ByVal control As Control, ByVal x As Integer)
        _synchro.Post(Sub()
                          control.Text = (x + 1).ToString
                      End Sub, x)
    End Sub


End Class
```
The result will be this.

[<img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2014/01/Synch2.png" alt="Synch2" width="300" height="300" class="alignnone size-full wp-image-2268" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2014/01/Synch2.png 300w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2014/01/Synch2-150x150.png 150w" sizes="(max-width: 300px) 100vw, 300px" />][1]

So for the send method this is 2 because the line with the return will only be executed after the send has completed. For the post method this will be one because the returnvalue gets set sometime in the future which will most likely be after the Return has been called. 

So something to be aware of.

 [1]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2014/01/Synch2.png