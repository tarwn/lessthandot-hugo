---
title: The conditional attribute
author: Christiaan Baes (chrissie1)
type: post
date: 2012-11-05T18:10:00+00:00
ID: 1781
excerpt: 'The conditional attribute can be much nicer than using #If DEBUG Then, but there is a gotcha.'
url: /index.php/desktopdev/mstech/the-conditional-attribute/
views:
  - 5749
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - conditional
  - vb.net

---
The conditional attribute is [described as follows by MSDN][1].

> The attribute Conditional enables the definition of conditional methods. The Conditional attribute indicates a condition by testing a conditional compilation symbol. Calls to a conditional method are either included or omitted depending on whether this symbol is defined at the point of the call. If the symbol is defined, the call is included; otherwise, the call (including evaluation of the parameters of the call) is omitted.

Now that sounds very complicated.

Lets take this little example.

```vbnet
Imports System.Threading

Module Module1

    Private incre As New Incrementing

    Sub Main()
        For i = 0 To 20
            count()
            count2()
        Next
        Console.ReadLine()
    End Sub

    &lt;Conditional("DEBUG")&gt;
    Sub count()
        For i = 1 To 1000
            incre.Add()
        Next
        Console.WriteLine(incre.Read())
    End Sub

    Sub count2()
        For i = 1 To 500
            incre.Add()
        Next
        Console.WriteLine(incre.Read())
    End Sub

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
When you run this in debug mode you will get.

> 1000
  
> 1500
  
> 2500
  
> 3000
  
> 4000
  
> 4500
  
> 5500
  
> 6000
  
> 7000
  
> 7500
  
> 8500
  
> 9000
  
> 10000
  
> 10500
  
> 11500
  
> 12000
  
> 13000
  
> 13500
  
> 14500
  
> 15000
  
> 16000
  
> 16500
  
> 17500
  
> 18000
  
> 19000
  
> 19500
  
> 20500
  
> 21000
  
> 22000
  
> 22500
  
> 23500
  
> 24000
  
> 25000
  
> 25500
  
> 26500
  
> 27000
  
> 28000
  
> 28500
  
> 29500
  
> 30000
  
> 31000
  
> 31500 

But when you run this in Release mode you will get this.

> 500
  
> 1000
  
> 1500
  
> 2000
  
> 2500
  
> 3000
  
> 3500
  
> 4000
  
> 4500
  
> 5000
  
> 5500
  
> 6000
  
> 6500
  
> 7000
  
> 7500
  
> 8000
  
> 8500
  
> 9000
  
> 9500
  
> 10000
  
> 10500 

So the attribute conditional means that the method you tag with the attribute will only run in the mode you tag it for.

So our method count will not run.

It is a much prettier way than writing this.

```vbnet
Imports System.Threading

Module Module1

    Private incre As New Incrementing

    Sub Main()
        For i = 0 To 20
#If debug Then
            count
            count2
#Else
            count2()
#End If
        Next
        Console.ReadLine()
    End Sub

    Sub count()
        For i = 1 To 1000
            incre.Add()
        Next
        Console.WriteLine(incre.Read())
    End Sub

    Sub count2()
        For i = 1 To 500
            incre.Add()
        Next
        Console.WriteLine(incre.Read())
    End Sub

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
But it doesn&#8217;t always work.

Look at this code for example.

```vbnet
Imports System.Threading

Module Module1

    Private incre As New Incrementing

    Sub Main()
        For i = 0 To 20
#If debug Then
            Dim t1 As New Thread(AddressOf count)
            t1.isbackground = True
            t1.start
            Dim t2 As New Thread(AddressOf count2)
            t2.isbackground = True
            t2.start
#Else
            Dim t2 As New Thread(AddressOf count2)
            t2.IsBackground = True
            t2.Start()
#End If
        Next
        Console.ReadLine()
    End Sub

    Sub count()
        For i = 1 To 1000
            incre.Add()
        Next
        Console.WriteLine(incre.Read())
    End Sub

    Sub count2()
        For i = 1 To 500
            incre.Add()
        Next
        Console.WriteLine(incre.Read())
    End Sub

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
We could replace that with this.

```vbnet
Imports System.Threading

Module Module1

    Private incre As New Incrementing

    Sub Main()
        For i = 0 To 20
            Dim t1 As New Thread(AddressOf count)
            t1.isbackground = True
            t1.start
            Dim t2 As New Thread(AddressOf count2)
            t2.isbackground = True
            t2.start
        Next
        Console.ReadLine()
    End Sub

    &lt;Conditional("DEBUG")&gt;
    Sub count()
        For i = 1 To 1000
            incre.Add()
        Next
        Console.WriteLine(incre.Read())
    End Sub

    Sub count2()
        For i = 1 To 500
            incre.Add()
        Next
        Console.WriteLine(incre.Read())
    End Sub

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
Looks a little bit better without the #If.

But the conditional method gets executed anyway, and that&#8217;s not what I expected it to do. 

Just try it.

I&#8217;m not sure if this is by design or not but I think it&#8217;s interesting.

Of course at such a time it is always nice if you can ask someone smarter (which is most people) than you why this happens and if it is by design. 

So I asked Lucian Wischik again. And within 5 minutes I got this answer.

> I think what you’re observing is basically this:

```vbnet
Module Module1

    Sub Main()

        test() ' this call is skipped in release builds

 

        Dim x As Action = AddressOf test ' VB: this works in release+debug builds. C#: is a compile-time error in release+debug builds

        x()

    End Sub

 

    &lt;Conditional("DEBUG")&gt;

    Sub test()

        Console.WriteLine("hello")

    End Sub

End Module
```
> It’s sort of elliptically implied from the first MSDN link,
> 
> Applying ConditionalAttribute to a method indicates to compilers that a call to the method should not be compiled into Microsoft intermediate language (MSIL) unless the conditional compilation symbol that is associated with ConditionalAttribute is defined.
> 
> The line "test()" is a call to the method and hence subject to the above paragraph. But the next line is taking-address-of (not subject to the paragraph) and the one after is call to the delegate’s invoke method (also not subject).
> 
> I think the VB MSDN page would be improved if it made this clear. (The C# page already makes it clear that you can’t take the address of a method in C# if that method has the conditional attribute). I’ll pass that on to the authors of the page.

 [1]: http://msdn.microsoft.com/en-us/library/aa664622(v=vs.71).aspx