---
title: 'Vb.Net: Addhandler and lambdas, watch out.'
author: Christiaan Baes (chrissie1)
type: post
date: 2011-11-18T06:29:00+00:00
ID: 1389
excerpt: "A problem with Addhandler and lambdas and why you can get in trouble if you're not careful."
url: /index.php/desktopdev/mstech/vb-net-addhandler-and-lambdas/
views:
  - 6501
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - event
  - lambda
  - vb.net

---
## The problem

Just look at this code. And try to figure out what&#8217;s wrong with it. Without watching the solution. You can even compile and run it without a problem.

```vbnet
Module Module1

    Sub Main()
        Dim testClass = New Class1
        Dim returnString = ""
        AddHandler testClass.OnSomething, Function(result) returnString = result
        testClass.Dosomething()
        Console.WriteLine(returnString)
        Console.ReadLine()
    End Sub

    Public Class Class1
        Public Event OnSomething(result As String)

        Public Sub Dosomething()
            RaiseEvent OnSomething("test")
        End Sub
    End Class

End Module```
The problem is that <code class="codespan">Console.WriteLine(returnString)</code> doesn&#8217;t write test as we would expect. And we know the event gets raised because we can easily debug that. And we know that debugging the lambda is a pain in the &#8230; so we don&#8217;t really know if that gets run. We just see that returnString never gets changed so we can expect it never to have run.

## The solution

Now try this.

```vbnet
Module Module1

    Sub Main()
        Dim testClass = New Class1
        Dim returnString = ""
        AddHandler testClass.OnSomething, Sub(result) returnString = result
        testClass.Dosomething()
        Console.WriteLine(returnString)
        Console.ReadLine()
    End Sub

    Public Class Class1
        Public Event OnSomething(result As String)

        Public Sub Dosomething()
            RaiseEvent OnSomething("test")
        End Sub
    End Class

End Module```
What changed? Not a lot. The lambda is now a Sub and no longer a Function. Apparently typesafety doesn&#8217;t include lambdas. Events are subs so your lambda should be a Sub to. If it is a function it does not throw an exception nor does it fail at compile time. It just silently ignores it, which isn&#8217;t funny.

So remember Events are Subs and not Functions.

<span class="MT_red">Update</span>

As people have said in the comments what is probably happening is this.

```vbnet
Module Module1

    Sub Main()
        Dim testClass = New Class1
        Dim returnString = ""
        AddHandler testClass.OnSomething, Function(result)
                                              Return returnString = result
                                          End Function
        testClass.Dosomething()
        Console.WriteLine(returnString)
        Console.ReadLine()
    End Sub

    Public Class Class1
        Public Event OnSomething(result As String)

        Public Sub Dosomething()
            RaiseEvent OnSomething("test")
        End Sub
    End Class

End Module```
Which I understand. But this still does not solve the problem why the compiler thinks an event can be a function. Seems to me some simple typechecking should have solved that.

<span class="MT_red">Update2</span>

And yes it is legal and called delegate relaxation.

And I will let Julian Wischik explain it.

> (1) A lambda written "Function(r) e" will ALWAYS assume that "e" is an expression.
> 
> And a lambda written "Sub(r) s" will ALWAYS assume that "s" is a statement.
> 
> So your hypothesis for what it’s doing is correct.
> 
> In other words: the decision for whether or not to interpret the thing as an EXPRESSION or STATEMENT is done solely by whether the keyword is "Function" or "Sub". It is not determined by the target delegate type.
> 
> (2) You wonder why it’s legal to assign a function-lambda to a void-returning-delegate-type.
> 
> Well, that’s always legal in VB. We call it "delegate relaxation". It is type-safe.
> 
> Here’s another example of delegate relaxation:
> 
> ```vbnet
Dim lambda = Function()
>   Return 1
> End Function
> Dim a As Action = lambda   ’ creates a wrapper which simply discards the returned value```
> Here’s another example of delegate relaxation:
> 
> ```vbnet
Dim lambda = Sub() Console.WriteLine("hello")
> Dim a As Action(Of Integer, String) = lambda   ’ here the wrapper discards the arguments```
> And here’s another example of that "wrapper which discards arguments"…
> 
> ```vbnet
Sub Button1Click() Handles Button1.Click```
> &#8212; here, the Button1.Click event has a delegate type with several parameters. We’re dropping them all implicitly.
> 
> &#8212;
  
> Lucian