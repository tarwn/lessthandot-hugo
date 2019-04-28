---
title: 'VB.Net: Guess the result'
author: Christiaan Baes (chrissie1)
type: post
date: 2012-07-29T14:47:00+00:00
ID: 1683
excerpt: Guess the result
url: /index.php/desktopdev/mstech/vbnet/vb-net-guess-the-result/
views:
  - 3777
categories:
  - VB.NET
tags:
  - vb.net

---
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
End Class
```
Before running the above try guessing the result. 

BTW it&#8217;s not a bug, it&#8217;s normal behavior.