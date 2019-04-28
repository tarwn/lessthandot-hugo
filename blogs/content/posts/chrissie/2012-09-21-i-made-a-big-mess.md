---
title: I made a big mess of my yield blogpost and I apologize to my readers for that.
author: Christiaan Baes (chrissie1)
type: post
date: 2012-09-21T05:24:00+00:00
ID: 1733
excerpt: I made a big mistake in my yield blogpost and I apologize to my readers for it.
url: /index.php/desktopdev/mstech/i-made-a-big-mess/
views:
  - 3126
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - vb.net
  - yield

---
I&#8217;m embarrassed, and not just a little. I mean a lot. 

[I made a blogpost][1] about the yield keyword and the fibonacci sequence and did not even check if I got the correct results. I just checked if the code ran. 

I was wrong and I was stupid. I would never do this in production. My main application has over 9k tests before I let it go in production. 

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/yield/yield1.png?mtime=1348211444"><img alt="" src="/wp-content/uploads/users/chrissie1/yield/yield1.png?mtime=1348211444" width="385" height="65" /></a>
</div>

But for my blogposts I sometimes go to quickly. 

It looks ok, hit Publish.

And then this happens.

> Great explanation of the Yield keyword. However, there is a logic error in the Fibs function of your &#8220;less pretty&#8221; code. Here is the output &#8230;1,1,2,4,8,16,32, etc&#8230; It should be &#8230; 1,1,2,3,5,8,13,etc&#8230; to be a true Fibonacci series. I tried including my fix for that but had trouble attaching the code so I gave up. 
> 
> Does this make me a Fibonazi? 😉

<span class="MT_red">I am ashamed.</span> I really am. And I say sorry to my readers. I should do better.

So I&#8217;ll make up for it.

Here are my tests.

```vbnet
Imports Nunit.FrameWork

Namespace DefaultNamespace
    Public Class TestFibonaci
        &lt;Test&gt;
        Public sub IfFirstNumberIs1
            Dim fibonacci = New Fibonacci().Result
            Assert.AreEqual(1, fibonacci(0))
        End Sub

        &lt;Test&gt;
        Public Sub IfSecondNumberIs1()
            Dim fibonacci = New Fibonacci().Result
            Assert.AreEqual(1, fibonacci(1))
        End Sub

        &lt;Test&gt;
        Public Sub IfThirdNumberIs2()
            Dim fibonacci = New Fibonacci().Result
            Assert.AreEqual(2, fibonacci(2))
        End Sub

        &lt;Test&gt;
        Public Sub IfFourthNumberIs3()
            Dim fibonacci = New Fibonacci().Result
            Assert.AreEqual(3, fibonacci(3))
        End Sub

        &lt;Test&gt;
        Public Sub IfFifthNumberIs5()
            Dim fibonacci = New Fibonacci().Result
            Assert.AreEqual(5, fibonacci(4))
        End Sub

        &lt;Test&gt;
        Public Sub IfSixthNumberIs8()
            Dim fibonacci = New Fibonacci().Result
            Assert.AreEqual(8, fibonacci(5))
        End Sub

        &lt;Test&gt;
        Public Sub IfSeventhNumberIs13()
            Dim fibonacci = New Fibonacci().Result
            Assert.AreEqual(13, fibonacci(6))
        End Sub
    End Class
End Namespace```
And here is the class with the code I published.

```vbnet
Namespace DefaultNamespace
    Public Class Fibonacci
        Public Iterator Function Result() As IEnumerable(Of Integer)
            Dim previous As Integer
            Dim current As Integer
            Yield 1
            Yield 1
            previous = 1
            current = 1
            While current &lt; Integer.MaxValue - previous
                current += previous
                previous = current
                Yield current
            End While
        End Function
    End Class
End NameSpace```
And this is what ncrunch tells me I should have seen before posting that piece of crap.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/yield/yield2.png?mtime=1348211659"><img alt="" src="/wp-content/uploads/users/chrissie1/yield/yield2.png?mtime=1348211659" width="537" height="292" /></a>
</div>

Oh man, that is so wrong. Let&#8217;s fix it.

```vbnet
Namespace DefaultNamespace
    Public Class Fibonacci
        Public Iterator Function Result() As IEnumerable(Of Integer)
            Dim previous As Integer
            Dim current As Integer
            Yield 1
            Yield 1
            previous = 1
            current = 1
            While current &lt; Integer.MaxValue - previous
                Dim tempprevious = current
                current += previous
                previous = tempprevious
                Yield current
            End While
        End Function
    End Class
End NameSpace```
And now I can be reasonably sure that it works as it should.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/yield/yield3.png?mtime=1348212035"><img alt="" src="/wp-content/uploads/users/chrissie1/yield/yield3.png?mtime=1348212035" width="529" height="289" /></a>
</div>

And now I can easily play with it and see that the shorter version gives me the same correct results.

```vbnet
Namespace DefaultNamespace
    Public Class Fibonacci
        Public Iterator Function Result() As IEnumerable(Of Integer)
            Yield 1
            Yield 1

            For Each n In Me.Result.Zip(Me.Result.Skip(1), Function(x, y) x + y)
                Yield n
            Next
        End Function
    End Class
End NameSpace```
Lesson learned.

And once again, SORRY.

 [1]: /index.php/DesktopDev/MSTech/new-in-vb11-the-yield