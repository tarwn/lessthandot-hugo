---
title: One task running and one only.
author: Christiaan Baes (chrissie1)
type: post
date: 2013-07-03T10:53:00+00:00
ID: 2129
excerpt: How to to have one task run in the background and cancel previous tasks.
url: /index.php/desktopdev/mstech/one-task-running-and-one/
views:
  - 3533
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - task
  - vb.net

---
I have a listbox on a form that gets some data from a service that is slow, I mean a whole 500ms slow. That is unacceptable. The data is not very critical so I can load it in the background. 

This easy.

```vbnet
Private Sub LoadList(ByVal id as Integer)
  Task.Run(Sub() 
    'Do something slow
    RaiseEvent TellTheUIWheAreReadyAndThatItHasNewData(ByVal newList as IList(Of CoolThing))
  End Sub)
End Sub```
The UI will update when the Task has run.

Of course when the user rapidly goes from one record to the next. It might see some strange things, since tasks will still run one by one. There is actually nothing that says the data will be the correct data, since we are using threads and we don&#8217;t know which thread will finish first. Yeah, threading is easy. 

So we would need to cancel our previous task if it is still running.

That is easy.

```vbnet
Private cts As CancellationTokenSource

        Private Sub LoadList(ByVal id As integer)
            If cts IsNot Nothing Then
                cts.Cancel()
            End If

            cts = New CancellationTokenSource

            Dim token = cts.Token
            Try
               Task.Run(Sub()
                   'Do something slow
                   token.ThrowIfCancellationRequested()
                   RaiseEvent TellTheUIWheAreReadyAndThatItHasNewData(ByVal newList as IList(Of CoolThing))
               End Sub, token)
            Catch ex As OperationCanceledException
               Log.Debug("Operation cancelled")
            End Try
        End Sub```
So I need a CancellationTokenSource which I need for it&#8217;s token and I pass that token to the task. 

Next time I run that code I will just be default cancel the task, if it is no longer running it won&#8217;t matter, And if it is it will cancel before it can inform the UI that it has something new or preferably somewhere before that, you can set that ThrowIfCancellationRequested anywhere you like. Or you could use the IsCancellationRequested from the token and forget about the Try/Catch around it. 

Something like this.

```vbnet
Private cts As CancellationTokenSource

        Private Sub LoadList(ByVal id As integer)
            If cts IsNot Nothing Then
                cts.Cancel()
            End If

            cts = New CancellationTokenSource

            Dim token = cts.Token
            Task.Run(Sub()
                   'Do something slow
                   If Not token.IsCancellationRequested() Then
                      RaiseEvent TellTheUIWheAreReadyAndThatItHasNewData(ByVal newList as IList(Of CoolThing))
                   End If
            End Sub, token)
        End Sub```
Get it? Good. Let&#8217;s move on.