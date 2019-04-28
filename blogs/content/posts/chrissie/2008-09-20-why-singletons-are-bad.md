---
title: Why singletons are bad
author: Christiaan Baes (chrissie1)
type: post
date: 2008-09-20T04:59:32+00:00
ID: 146
url: /index.php/architect/designingsoftware/why-singletons-are-bad/
views:
  - 5409
categories:
  - Designing Software

---
I didn&#8217;t know that google actually had a policy about it, [but they explain very well why you should avoid them][1]. They even have an application that searches for singletons in code.

Here is a little extract of that article.

> The use of singletons is actually a fairly controversial subject in the Java community; what was once an often-used design pattern is now being looked at as a less than desirable coding practice. The problem with singletons is that they introduce global state into a program, allowing anyone to access them at anytime (ignoring scope). Even worse, singletons are one of the most overused design patterns today, meaning that many people introduce this possibly detrimental global state in instances where it isn&#8217;t even necessary. What&#8217;s wrong with singletons&#8217; use of global state?

Of course they don&#8217;t really say how to replace them and when it really could be useful that I guess is up to you and your common sense. But if you have more then 1% of singletons in your application I guess your overusing it. 1% being an arbitrary number that could be less ;-).

I know I removed all of them from my application. You must remember that singletons are probably not going to be cleaned up by the GC because there is always that reference there so they eat some memory. Is that a bad thing? Perhaps if you have enough of them. And they can slow down a multithreaded application. 

Now you could say that instantiating a class takes time but does it really? Here is a little test.

I start with an interface 

```vbnet
Public Interface ITest
    Function Name() As String
End Interface```
then 2 classes 

one normal one 😉

```vbnet
Public Class Test
    Implements ITest

    Public Sub New()

    End Sub

    Public Function Name() As String Implements ITest.Name
        Return "test"
    End Function
End Class
```
and one singleton one.

```vbnet
Public Class Test2
    Implements ITest

#Region " Singleton Pattern Support "
    Private Shared SyncRoot As New Object
    Private Shared Instance As Test2

    Private Sub New()

    End Sub

    Public Shared ReadOnly Property getInstance() As Test2
        Get
            SyncLock SyncRoot
                If Instance Is Nothing Then
                    Instance = New Test2
                End If
                Return Instance
            End SyncLock
        End Get
    End Property
#End Region


    Public Function Name() As String Implements ITest.Name
        Return "test2"
    End Function
End Class```
And now the testcode

```vbnet
Module Module1

    Sub Main()
        Dim _stopwatch As New Stopwatch
        _stopwatch.Start()
        For Int As Integer = 0 To 10000000
            Dim _test As New Test
        Next
        _stopwatch.Stop()
        Console.WriteLine(_stopwatch.ElapsedMilliseconds)
        _stopwatch.Reset()
        _stopwatch.Start()
        For Int As Integer = 0 To 10000000
            Dim _test As Test2 = Test2.getInstance
        Next
        _stopwatch.Stop()
        Console.WriteLine(_stopwatch.ElapsedMilliseconds)
        _stopwatch.Reset()
        _stopwatch.Start()
        For Int As Integer = 0 To 10000000
            Dim _test As New Test
        Next
        _stopwatch.Stop()
        Console.WriteLine(_stopwatch.ElapsedMilliseconds)
        _stopwatch.Reset()
        _stopwatch.Start()
        For Int As Integer = 0 To 10000000
            Dim _test As Test2 = Test2.getInstance
        Next
        _stopwatch.Stop()
        Console.WriteLine(_stopwatch.ElapsedMilliseconds)
        Console.ReadLine()
    End Sub

End Module
```
And here are the results of the Belgian jury (me)

> 324
  
> 2493
  
> 221
  
> 2348

So the singleton is a whole lot slower. Not that you will notice in your application. But perhaps, who knows, this migth be important to you.

 [1]: http://code.google.com/p/google-singleton-detector/wiki/WhySingletonsAreControversial