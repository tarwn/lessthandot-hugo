---
title: Does Async-Await make the simple things easier?
author: Christiaan Baes (chrissie1)
type: post
date: 2012-11-04T04:00:00+00:00
ID: 1779
excerpt: Using Async-Await to do the simple things.
url: /index.php/desktopdev/mstech/does-async-await-make-the/
views:
  - 2763
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - multithreading
  - vb.net

---
So yesterday we came to the conclusion that [even simple things can be more difficult][1] than we think when threads are involved.

In our case we had a class that incremented a value and one instance of that class was being invoked by multiple threads. And we found out that even i+= 1 is not an atomic operation. 

But what happens when you swap the threads for an Async-Await syntax, Does it become more easy.

Apparently it isn&#8217;t easier for me. It took me quit a bit longer to find the right way of writing this.

Firstly you need to look at this.

```vbnet
Imports System.Threading

Module Module1

    Private incre As New Incrementing

    Sub Main()
        Console.WriteLine("Starting")
        Const numberofthreads As Integer = 20
        Dim ts(numberofthreads) As Task(Of Integer)
        For i = 0 To numberofthreads
            ts(i) = count()
        Next
        Task.WaitAll(ts)
        For i = 0 To numberofthreads
            Console.WriteLine(ts(i).Result)
        Next
        Console.ReadLine()
    End Sub

    Async Function count() As Task(Of Integer)
        For i = 1 To 1000
            Await incre.Add()
            Thread.Sleep(1)
        Next
        Return incre.Read()
    End Function

    Public Class Incrementing
        Private i As Integer = 0

        Public Async Function Add() As Task(Of Integer)
            i += 1
            Return 0
        End Function

        Public Function Read() As Integer
            Return i
        End Function
    End Class
End Module```
Do you think the above runs Async?

You probably guessed it. It doesn&#8217;t.

As you can see by this result.

> Starting
  
> 1000
  
> 2000
  
> 3000
  
> 4000
  
> 5000
  
> 6000
  
> 7000
  
> 8000
  
> 9000
  
> 10000
  
> 11000
  
> 12000
  
> 13000
  
> 14000
  
> 15000
  
> 16000
  
> 17000
  
> 18000
  
> 19000
  
> 20000
  
> 21000

It&#8217;s all nice and sync. 

The problem here is the for each in the count method. We can add a Task.Run to that do make it do what we want.

So second attempt.

```vbnet
Imports System.Threading

Module Module1

    Private incre As New Incrementing

    Sub Main()
        Console.WriteLine("Starting")
        Const numberofthreads As Integer = 20
        Dim ts(numberofthreads) As Task(Of Integer)
        For i = 0 To numberofthreads
            ts(i) = count()
        Next
        Task.WaitAll(ts)
        For i = 0 To numberofthreads
            Console.WriteLine(ts(i).Result)
        Next
        Console.ReadLine()
    End Sub

    Async Function count() As Task(Of Integer)
        Await Task.Run(Async Function()
                           For i = 1 To 1000
                               Await incre.Add()
                               Thread.Sleep(1)
                           Next
                       End Function)
        Return incre.Read()
    End Function

    Public Class Incrementing
        Private i As Integer = 0

        Public Async Function Add() As Task(Of Integer)
            Await Task.Run(Sub() i += 1)
            Return 0
        End Function

        Public Function Read() As Integer
            Return i
        End Function
    End Class
End Module```
The result however is wrong

> Starting
  
> 14824
  
> 16243
  
> 10212
  
> 10336
  
> 15125
  
> 14166
  
> 11188
  
> 13141
  
> 17146
  
> 16582
  
> 16945
  
> 13803
  
> 15913
  
> 17088
  
> 17189
  
> 20408
  
> 20426
  
> 20309
  
> 20930
  
> 20741
  
> 20896

So it is very easy to find all kinds of ways that don&#8217;t work and give freaky results.

Actually getting the result I wanted wasn&#8217;t that difficult.

But it still required using a lock.

This one will get us the correct result.

```vbnet
Imports System.Threading

Module Module1

    Private incre As New Incrementing

    Sub Main()
        Console.WriteLine("Starting")
        Const numberofthreads As Integer = 20
        Dim ts(numberofthreads) As Task(Of Integer)
        For i = 0 To numberofthreads
            ts(i) = count()
        Next
        Task.WaitAll(ts)
        For i = 0 To numberofthreads
            Console.WriteLine(ts(i).Result)
        Next
        Console.ReadLine()
    End Sub

    Async Function count() As Task(Of Integer)
        Await Task.Run(Sub()
                           For i = 1 To 1000
                               incre.Add()
                               Thread.Sleep(1)
                           Next
                       End Sub)
        Return incre.Read()
    End Function

    Public Class Incrementing
        Private i As Integer = 0

        Public Sub Add()
            Interlocked.Increment(i)
        End Sub

        Public Function Read() As Integer
            Return i
        End Function
    End Class
End Module```
With this as one of the possible results.

> Starting
  
> 8442
  
> 8490
  
> 8504
  
> 8495
  
> 8494
  
> 8541
  
> 8534
  
> 8531
  
> 13026
  
> 17454
  
> 17496
  
> 17504
  
> 17490
  
> 17496
  
> 17527
  
> 17527
  
> 17504
  
> 19515
  
> 20993
  
> 21000
  
> 21000

Where it is important to note that we are only interested in the end result and not on how it gets there. 

Threading is lots of fun as you can see. And getting it right can be non-trivial.

 [1]: /index.php/DesktopDev/MSTech/threads-are-always-tricky-even