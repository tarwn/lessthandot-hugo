---
title: Threads are always tricky, even for simple things, or you might think they are simple anyway.
author: Christiaan Baes (chrissie1)
type: post
date: 2012-11-03T07:05:00+00:00
ID: 1778
excerpt: Threads are always tricky, even for simple things, or you might think they are simple anyway. Incrementation is one of those little things that will get you.
url: /index.php/desktopdev/mstech/threads-are-always-tricky-even/
views:
  - 2239
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - multithreading
  - vb.net

---
I know you all read [8 things you probably didn’t know about C#][1]. But there&#8217;s a million things about threads you didn&#8217;t know and then some would be a nice title for a blogpost too. 

It&#8217;s this little line that should draw your attention.

> i = 1 is atomic (thread-safe) for an int but not long

And that is good to know (for 32-bit systems anyway). 

But don&#8217;t mistake the above with this line.

> i += 1

That line is not threadsafe.

You can easily verify that by doing this.

```vbnet
Imports System.Threading

Module Module1

    Private incre As New Incrementing

    Sub Main()
        For i = 1 To 10
            Dim t1 As New Thread(AddressOf count)
            t1.IsBackground = True
            t1.Start()
            Dim t2 As New Thread(AddressOf count)
            t2.IsBackground = True
            t2.Start()
        Next
        Console.ReadLine()
    End Sub

    Sub count()
        For i = 1 To 1000
            incre.Add()
            Thread.Sleep(1)
        Next
        Console.WriteLine(incre.Read())
    End Sub

    Public Class Incrementing
        Private i As Integer = 0

        Public Sub Add()
            i += 1
        End Sub

        Public Function Read() As Integer
            Return i
        End Function
    End Class
End Module
```
You would expect to get to 20000 here because you are doing 20000 time i += 1, right.

Well no.

> 18342
  
> 18359
  
> 18404
  
> 18437
  
> 18444
  
> 18444
  
> 18444
  
> 18473
  
> 18479
  
> 18490
  
> 18490
  
> 18491
  
> 18494
  
> 18514
  
> 18520
  
> 18520
  
> 18522
  
> 18525
  
> 18525
  
> 18527 

this was one of the results I got. 

Doesn&#8217;t make sense?

Well sure it does.

because i += 1 is actually 3 operations (at least).

Read i, add 1 to i and then store i. And I did not take that into account. 

So at several points in time two or more threads might be reading the same value of i and thus we never get to 20000.

There are of course several ways around this.

We could synclock the i += 1 operation.

```vbnet
Public Sub Add()
            SyncLock lockobject
                i += 1
            End SyncLock
        End Sub```
or we could use Interlocked.Increment(i)

```vbnet
Public Sub Add()
            Interlocked.Increment(i)
        End Sub```
And now the results are more inline with what I expected.

> 19857
  
> 19863
  
> 19877
  
> 19887
  
> 19902
  
> 19906
  
> 19932
  
> 19932
  
> 19941
  
> 19948
  
> 19955
  
> 19960
  
> 19967
  
> 19974
  
> 19989
  
> 19992
  
> 19995
  
> 19997
  
> 19999
  
> 20000

 [1]: http://damieng.com/