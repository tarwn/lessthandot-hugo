---
title: New in VB11, the yield keyword
author: Christiaan Baes (chrissie1)
type: post
date: 2012-09-18T11:53:00+00:00
ID: 1726
excerpt: "VB.Net has the Yield keyword but it doesn't work like it does in C#. Which is a shame."
url: /index.php/desktopdev/mstech/new-in-vb11-the-yield/
views:
  - 11525
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - vb.net
  - yield

---
UPDATE 2: you should also read this after you read the below blogpost. [I made a big mess of this yield blogpost and I apologize to my readers for that.][1]

Yes it is finally here. The yield keyword has come to VB.Net. It has been in C# for a while. This is all in attempt, no doubt, to make C# and VB.Net a bit more compatible. T annoy us less when you have to use the other language. To make our lives easier and more comfortable. Alas they failed again.

Via twitter I found [this little paste2][2] with an interesting approach to solve the Fibonacci thing using the Yield keyword.

This was the tweet.

> David Siegel &#8207;@dvdsgl
  
> A friend was asked to produce the fibonacci sequence in C# during an interview. Here&#8217;s a cute, very slow impl: http://paste2.org/p/2236575 

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace ConsoleApplication5
{
    class Program
    {
        static void Main(string[] args)
        {
            foreach(int fib in Fibs)
            {
                Console.WriteLine(fib);
            }
            Console.ReadLine();
        }

        public static IEnumerable&lt;int&gt; Fibs
        {
            get
            {
                yield return 1;
                yield return 1;

                foreach (var n in Fibs.Zip(Fibs.Skip(1), (x, y) =&gt; x + y))
                    yield return n;
            }
        }
    }
}
```
I thought it was cute and was looking to replicate in VB.Net, as I want to learn new things and be less stupid.

And this would be the translation of the above.

```vbnet
Module Module1
    
    Sub Main()
        For Each fib In Fibs
            Console.WriteLine(fib)
        Next
        Console.ReadLine()
    End Sub

    Private ReadOnly Iterator Property Fibs() As IEnumerable(Of Integer)
        Get
            Yield 1
            Yield 1

            For Each n In Fibs.Zip(Fibs.Skip(1), Function(x, y) x + y)
                Yield n
            Next
        End Get
    End Property
    
End Module
```
Ain&#8217;t that cool? No. Because you get two errors.

> The implicit return variable of an Iterator or Async method cannot be accessed.

It is referring to the Fibs in Fibs.Zip and Fibs.Skip.

Oh darnit.

But not to worry I have a less pretty solution.

```vbnet
Module Module1
    
    Sub Main()
        For Each fib In Fibs
            Console.WriteLine(fib)
        Next
        Console.ReadLine()
    End Sub

    Private Iterator Function Fibs() As IEnumerable(Of Integer)
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

End Module
```
My solution is however much faster. So there, take that.

<span class="MT_red">Update: In the comments below Damien points out that VB is merely confused and needs a little push in the right direction. As seen below.</span>

```vbnet
Module Module1

    Sub Main()
        For Each fib In Fibs
            Console.WriteLine(fib)
        Next
        Console.ReadLine()
    End Sub

    Private ReadOnly Iterator Property Fibs() As IEnumerable(Of Integer)
        Get
            Yield 1
            Yield 1

            For Each n In Module1.Fibs.Zip(Module1.Fibs.Skip(1), Function(x, y) x + y)
                Yield n
            Next
        End Get
    End Property

End Module```
Because in VB you can still do this.

```vbnet
Module Module1

    Sub Main()
        For Each fib In Fibs
            Console.WriteLine(fib)
        Next
        Console.ReadLine()
    End Sub

    Private ReadOnly Property Fibs() As IEnumerable(Of Integer)
        Get
            Fibs = {1, 1, 2}
        End Get
    End Property

End Module```
So I&#8217;m stupid, sue me.

Disclaimer: his post is full of sarcasm and other kinds of humor you might not understand.

 [1]: /index.php/DesktopDev/MSTech/i-made-a-big-mess
 [2]: http://paste2.org/p/2236575