---
title: 'VB.Net: Guess the result – The explanation'
author: Christiaan Baes (chrissie1)
type: post
date: 2012-07-30T07:11:00+00:00
ID: 1684
excerpt: Trying to explain what is going on with the Byref and Byval.
url: /index.php/desktopdev/mstech/vbnet/vb-net-guess-the-result-1/
views:
  - 2823
categories:
  - VB.NET
tags:
  - byref
  - byval
  - vb.net

---
SO yesterday (on a Sunday) I asked you to look a this code. And guess what it did.

```vbnet
Module Module1

    Sub Main()
        Dim c1 As New class1
        Console.WriteLine(c1.Name)
        c1.Name = "test1"
        test1(c1)
        Console.WriteLine(c1.Name)
        c1.Name = "test1"
        test2(c1)
        Console.WriteLine(c1.Name)
        Console.ReadLine()
    End Sub

    Public Sub test1(ByRef c1 As class1)
        c1 = New class1
    End Sub

    Public Sub test2(ByVal c1 As class1)
        c1 = New class1
    End Sub

End Module

Public Class class1
    Public Property Name As String
End Class```
And in essence you should now the difference between ByVal and ByRef for reference types.

The result would be this.

> test1

For ByRef the result is easy to understand since you are just passing the reference of that object to the method and the method than just goes on working with your object like it did in the passing method.

The reference for the argument passed as ByVal is a copy of that Reference and does not allow you to change the value. So when you try to create a new object it will refuse that because you are trying to change the reference. 

Now try this piece of code.

```vbnet
Module Module1

    Sub Main()
        Dim c1 As New class1
        Console.WriteLine(c1.Name)
        c1.Name = "test1"
        test1(c1)
        Console.WriteLine(c1.Name)
        c1.Name = "test1"
        test2(c1)
        Console.WriteLine(c1.Name)
        Console.ReadLine()
    End Sub

    Public Sub test1(ByRef c1 As class1)
        c1 = New class1
        c1.Name = "test2"
        Console.WriteLine(c1.Name)

    End Sub

    Public Sub test2(ByVal c1 As class1)
        c1 = New class1
        c1.Name = "test3"
        Console.WriteLine(c1.Name)
    End Sub

End Module

Public Class class1
    Public Property Name As String
End Class```
With this as the result.

> test2
  
> test2
  
> test3
  
> test1

It shows that the c1 in the method test2 has become a different variable which is no longer associated with our passing parameter since they no longer have the same reference.

However.

```vbnet
Module Module1

    Sub Main()
        Dim c1 As New class1
        Console.WriteLine(c1.Name)
        c1.Name = "test1"
        test1(c1)
        Console.WriteLine(c1.Name)
        c1.Name = "test1"
        test2(c1)
        Console.WriteLine(c1.Name)
        Console.ReadLine()
    End Sub

    Public Sub test1(ByRef c1 As class1)
        c1.Name = "test2"
    End Sub

    Public Sub test2(ByVal c1 As class1)
        c1.Name = "test3"
    End Sub

End Module

Public Class class1
    Public Property Name As String
End Class```
Will give the following result.

> test2
  
> test3 

The c1 passed to test2 as Byval now stiil behaves the same because the underlying reference is still the same.

So ByVal creates a copy of the reference but until the point you actually change that reference it will still point to the same object.

This is all of course perfectly logical and all, but I&#8217;m sure it has caused more then a few bugs for people.