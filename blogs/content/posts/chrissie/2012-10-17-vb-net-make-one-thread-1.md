---
title: VB.Net make one thread wait for the next now with Async Await
author: Christiaan Baes (chrissie1)
type: post
date: 2012-10-17T06:24:00+00:00
ID: 1759
excerpt: Letting one thread wait for another one using Async Await.
url: /index.php/desktopdev/mstech/vb-net-make-one-thread-1/
views:
  - 11024
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - threading
  - vb.net

---
In my [previous post][1] I showed you how to use threads wait fro one another.

But of course in VB11 we can also use Async Await out of the box.

First of all let&#8217;s reproduce what we had with Async Await. I came up with this. I did not try this with lambdas (not even sure that would be possible for now).

```vbnet
Imports System.Threading

Module Module1

    Sub Main()
        Method1()
        Method2()
        Console.ReadLine()
    End Sub

   Private Async Function Method1() As Task
        Console.WriteLine("waiting")
        Await Task.Delay(2000)
        Console.WriteLine(1)
    End Function

    Private Async Function Method2() As Task
        Console.WriteLine(2)
    End Function

End Module

```
That gives us.

> waiting
  
> 2
  
> 1

And now how do I tell it to do this in order?

Simples, I create a function that calls the other two in the order I need them. 

Like this.

```vbnet
Imports System.Threading

Module Module1

    Sub Main()
        DoThings()
        Console.ReadLine()
    End Sub

    Private Async Function DoThings() As Task
        Await Method1()
        Await Method2()
    End Function

    Private Async Function Method1() As Task
        Console.WriteLine("waiting")
        Await Task.Delay(2000)
        Console.WriteLine(1)
    End Function

    Private Async Function Method2() As Task
        Console.WriteLine(2)
    End Function

End Module

```
And magic happens and you get this.

> waiting
  
> 1
  
> 2

Truly magic.

 [1]: VB.Net make one thread wait for the next