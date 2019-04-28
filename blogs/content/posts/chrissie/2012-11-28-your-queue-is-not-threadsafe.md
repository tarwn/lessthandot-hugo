---
title: Your queue is not threadsafe and sometimes that can give unwanted sideeffects
author: Christiaan Baes (chrissie1)
type: post
date: 2012-11-28T06:58:00+00:00
ID: 1805
excerpt: The queue is not threadsafe, but we have the concurrentqueue to help us out when needed.
url: /index.php/desktopdev/mstech/your-queue-is-not-threadsafe/
views:
  - 11327
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - threading
  - vb.net

---
## Introduction

For one of my projects I needed to show the 30 last messages to the user. I was not interested in keeping these messages. So I decided to use a queue. The messages come from different sources and different threads. 

And the different threads bit is important. 

## The queue

Here is what can happen if you use the not threadsafe queue.

Let&#8217;s take this little example.

```vbnet
Imports System.Threading

Module Module1
    Private _queue As Queue(Of String)
    Private _count As Integer

    Sub Main()
        Console.WriteLine("Not concurrent threads")
        _count = 0
        Dim t As New Thread(New ParameterizedThreadStart(Sub() Addmessages(Sub(x) AddMessage(x))))
        Dim t2 As New Thread(New ParameterizedThreadStart(Sub() Addmessages(Sub(x) AddMessage(x))))
        Dim t3 As New Thread(New ParameterizedThreadStart(Sub() Addmessages(Sub(x) AddMessage(x))))
        Dim t4 As New Thread(New ParameterizedThreadStart(Sub() Addmessages(Sub(x) AddMessage(x))))
        t.Start()
        t2.Start()
        t3.Start()
        t4.Start()
        Do While _count &lt; 4

        Loop
        _count = 1
        For Each q In _queue.Reverse.ToList
            Console.WriteLine(_count & ". " & q)
            _count += 1
        Next
        Console.ReadLine()
    End Sub

    Public Sub Addmessages(ByVal action As Action(Of String))
        For x = 1 To 1000000
            action.Invoke("test")
        Next
        _count += 1
    End Sub

    Public Sub AddMessage(message As String)
        If _Queue Is Nothing Then
            _queue = New Queue(Of String)(1000000)
        End If
        If Not String.IsNullOrEmpty(message) Then
            _Queue.Enqueue(message)
        End If
        Dim result As String
        If _queue.Count &gt; 10 Then
            _queue.Dequeue()
        End If
    End Sub

End Module```
That is 4 threads that add 1000000 messages to the queue at an alarming rate. And I only want to keep the last 10 messages in my queue.

The result. 

> 128683. test
  
> 128684. test
  
> 128685. test
  
> 128686. test
  
> 128687. test
  
> 128688. test
  
> 128689. test
  
> 128690.
  
> 128691. test
  
> 128692. test
  
> 128693. test
  
> 128694. test
  
> 128695. test
  
> 128696. test
  
> 128697. test
  
> 128698. test
  
> 128699. test
  
> 128700. test
  
> 128701. test
  
> 128702. test
  
> 128703. test
  
> 128704. test

Those are only the last ones. There are 128704 elements in our queue and some are empty. Not sure how I get the empty ones but I guess it must be somewhere between an enqueue and a dequeue. 

Anyway the results are not what we desire. 

And of course now I could start adding blocks here and there to make sure the enqueue and dequeue are atomic operations and what not but Since .Net 4.0 there are Concurent collections and they are threadsafe by design. Lucky us.

## The concurrent queue

We can simply change our code to this.

```vbnet
Imports System.Collections.Concurrent
Imports System.Threading

Module Module1
    Private _concurrentQueue As ConcurrentQueue(Of String)
    Private _count As Integer

    Sub Main()
        Console.WriteLine("concurrent threads")
        _count = 0
        Dim t5 As New Thread(New ParameterizedThreadStart(Sub() Addmessages(Sub(x) AddMessageConcurrently(x))))
        Dim t6 As New Thread(New ParameterizedThreadStart(Sub() Addmessages(Sub(x) AddMessageConcurrently(x))))
        Dim t7 As New Thread(New ParameterizedThreadStart(Sub() Addmessages(Sub(x) AddMessageConcurrently(x))))
        Dim t8 As New Thread(New ParameterizedThreadStart(Sub() Addmessages(Sub(x) AddMessageConcurrently(x))))
        t5.Start()
        t6.Start()
        t7.Start()
        t8.Start()
        Do While _count &lt; 4

        Loop
        _count = 1
        For Each q In _concurrentQueue.Reverse.ToList
            Console.WriteLine(_count & ". " & q)
            _count += 1
        Next
        Console.ReadLine()
    End Sub

    Public Sub Addmessages(ByVal action As Action(Of String))
        For x = 1 To 1000000
            action.Invoke("test")
        Next
        _count += 1
    End Sub

    Public Sub AddMessageConcurrently(message As String)
        If _concurrentQueue Is Nothing Then
            _concurrentQueue = New ConcurrentQueue(Of String)()
        End If
        If Not String.IsNullOrEmpty(message) Then
            _concurrentQueue.Enqueue(message)
        End If
        Dim result As String
        If _concurrentQueue.Count &gt; 10 Then
            _concurrentQueue.TryDequeue(result)
        End If
    End Sub

End Module
```
And now the result is.

> concurrent threads
  
> 1. test
  
> 2. test
  
> 3. test
  
> 4. test
  
> 5. test
  
> 6. test
  
> 7. test
  
> 8. test
  
> 9. test
  
> 10. test

And that is exactly what I expected.

## Conclusion

There is a namespace in .Net since 4.0 that contains threadsafe collections. And it is there for you to use. I would however not overuse it since I expect the blocking to have an effect on performance.