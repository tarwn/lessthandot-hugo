---
title: VB.Net make one thread wait for the next
author: Christiaan Baes (chrissie1)
type: post
date: 2012-10-17T05:31:00+00:00
ID: 1758
excerpt: How to make one thread wait for the next one to complete.
url: /index.php/desktopdev/mstech/vb-net-make-one-thread/
views:
  - 7702
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - threading
  - vb.net

---
Yesterday we got [a question][1] on the [VBIB.Be][2] forum (dutch) about threading.

The OP wanted to have one thread wait for the next.

The problem with threading is that it is complicated. And that there a re more than one way to solve your problem. And the optimal solution highly depends on the specific problem at hand. The solution to his problem, have 1 thread wait on 1 other thread until the 1st thread is finished doing what it must be do, is pretty simple and straight forward. And I know you all want me to tell you about it.

In short I got the problem down to this.

```
Imports System.Threading

Module Module1

	Sub Main()
		Dim t1 = New Thread(New ThreadStart(Sub()
												Console.WriteLine("waiting")
												Thread.Sleep(2000)
												Console.WriteLine(1)
											End Sub))
		Dim t2 = New Thread(New ThreadStart(Sub()
												Console.WriteLine(2)
											End Sub))
		t1.Start()
		t2.Start()
		Console.ReadLine()
	End Sub

End Module

```
The result will in most cases be.

> waiting
  
> 2
  
> 1 

Of course this is threading so in some cases the above result might be different. But in 99% of the case it will be the above, unless you are really unlucky.

My presented solution is to use a ManualResetEvent and a Waitone.

Like this.

```vbnet
Imports System.Threading

Module Module1

	Sub Main()
		Dim syncEvent = New ManualResetEvent(False)
		Dim t1 = New Thread(New ThreadStart(Sub()
												Console.WriteLine("waiting")
												Thread.Sleep(2000)
												Console.WriteLine(1)
												syncEvent.Set()
											End Sub))
		Dim t2 = New Thread(New ThreadStart(Sub()
												syncEvent.WaitOne()
												Console.WriteLine(2)
											End Sub))
		t1.Start()
		t2.Start()
		Console.ReadLine()
	End Sub

End Module

```
The first thing to note Is the syncEvent.Set()

You add this **behind** the code you want the other thread to wait for. And then you also need a syncEvent.WaitOne() this is the place where you want to continue after the code before the Set has done processing.

So now we will probably get this.

> waiting
  
> 1
  
> 2 

And that is the desired effect.

And if my explanation was any good you should be able to predict, more or less what this will do.

```vbnet
Imports System.Threading

Module Module1

	Sub Main()
		Dim syncEvent = New ManualResetEvent(False)
		Dim t1 = New Thread(New ThreadStart(Sub()
												Console.WriteLine("waiting")
												Thread.Sleep(2000)
												Console.WriteLine(1)
												syncEvent.Set()
												Console.WriteLine(3)
											End Sub))
		Dim t2 = New Thread(New ThreadStart(Sub()
												Console.WriteLine(4)
												syncEvent.WaitOne()
												Console.WriteLine(2)
											End Sub))
		t1.Start()
		t2.Start()
		Console.ReadLine()
	End Sub

End Module

```
Happy! threading

 [1]: http://www.vbib.be/index.php?/topic/11615-threading
 [2]: http://www.vbib.be