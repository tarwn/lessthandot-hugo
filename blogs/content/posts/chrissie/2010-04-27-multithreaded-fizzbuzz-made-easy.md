---
title: Multithreaded FizzBuzz made easy
author: Christiaan Baes (chrissie1)
type: post
date: 2010-04-27T09:43:07+00:00
ID: 772
url: /index.php/desktopdev/mstech/multithreaded-fizzbuzz-made-easy/
views:
  - 6218
categories:
  - Microsoft Technologies
tags:
  - fizzbuzz
  - parallel
  - vb.net

---
This week I&#8217;m trying to find all the new thing in .Net 4.0 and VS2010. One of the more exiting features in .Net 4.0 is the parallel extension. Or how to make multithreading easy. 

I guess we all know the Fizzbuzz test by now.

Describe by [Imram][1] as the following.

> Write a program that prints the numbers from 1 to 100. But for multiples of three print &#8220;Fizz&#8221; instead of the number and for the multiples of five print &#8220;Buzz&#8221;. For numbers which are multiples of both three and five print &#8220;FizzBuzz&#8221;. 

Seems easy enough.

```vbnet
Sub Main()
        Dim fiz As Integer = 3
        Dim buzz As Integer = 5
        Dim input As String
        Dim x As Integer
        Console.WriteLine("Give the upper limit:")
        input = Console.ReadLine()
        If Integer.TryParse(input, x) AndAlso x &gt; 15 Then
            Dim text As String = String.Empty
            For i = 1 To x
                text = i & ". "
                If i Mod 3 = 0 Then
                    text &= "fizz"
                End If
                If i Mod 5 = 0 Then
                    If text.Contains("fizz") Then
                        text &= " "
                    End If
                    text &= "buzz"
                End If
                If Not text.Contains("zz") Then
                    text &= i
                End If
                Console.WriteLine(text)
            Next
        End If
        Console.ReadLine()
    End Sub```
With this as the result.

> 1. 1
  
> 2. 2
  
> 3. fizz
  
> 4. 4
  
> 5. buzz
  
> 6. fizz
  
> 7. 7
  
> 8. 8
  
> 9. fizz
  
> 10. buzz
  
> 11. 11
  
> 12. fizz
  
> 13. 13
  
> 14. 14
  
> 15. fizz buzz
  
> 16. 16
  
> 17. 17
  
> 18. fizz
  
> 19. 19
  
> 20. buzz

But did anyone say you had to write these out in order? No they didn&#8217;t so we can make this much faster by using multithreading.

This is how I would do that in pre .Net 4.0.

```vbnet
Sub Main()
        Dim fiz As Integer = 3
        Dim buzz As Integer = 5
        Dim input As String
        Dim x As Integer
        Console.WriteLine("Give the upper limit:")
        input = Console.ReadLine()
        If Integer.TryParse(input, x) AndAlso x &gt; 15 Then
            For i = 1 To x
                Dim starter = New Threading.ParameterizedThreadStart(AddressOf Printtext)
                Dim t As New Threading.Thread(starter)
                t.Start(i)
            Next
        End If
        Console.ReadLine()
    End Sub

    Private Sub Printtext(ByVal o As Object)
        Dim i = CType(o, Integer)
        Dim text As String = String.Empty
        text = i & ". "
        If i Mod 3 = 0 Then
            text &= "fizz"
        End If
        If i Mod 5 = 0 Then
            If text.Contains("fizz") Then
                text &= " "
            End If
            text &= "buzz"
        End If
        If Not text.Contains("zz") Then
            text &= i
        End If
        Console.WriteLine(text)
    End Sub```
With this as the result.

> 1. 1
  
> 3. fizz
  
> 2. 2
  
> 4. 4
  
> 5. buzz
  
> 7. 7
  
> 9. fizz
  
> 8. 8
  
> 6. fizz
  
> 12. fizz
  
> 10. buzz
  
> 11. 11
  
> 13. 13
  
> 14. 14
  
> 15. fizz buzz
  
> 16. 16
  
> 17. 17
  
> 18. fizz
  
> 19. 19
  
> 20. buzz

Didin&#8217;t seem to difficult to me.

But it is even easier in .Net 4.0.

```vbnet
Sub Main()
        Dim fiz As Integer = 3
        Dim buzz As Integer = 5
        Dim input As String
        Dim x As Integer
        Console.WriteLine("Give the upper limit:")
        input = Console.ReadLine()
        If Integer.TryParse(input, x) AndAlso x &gt; 15 Then
            Dim text As String = String.Empty
            Parallel.For(1, x, Function(i) As Integer
                                   text = i & ". "
                                   If i Mod 3 = 0 Then
                                       text &= "fizz"
                                   End If
                                   If i Mod 5 = 0 Then
                                       If text.Contains("fizz") Then
                                           text &= " "
                                       End If
                                       text &= "buzz"
                                   End If
                                   If Not text.Contains("zz") Then
                                       text &= i
                                   End If
                                   Console.WriteLine(text)
                                   Return 0
                               End Function)
        End If
        Console.ReadLine()
    End Sub```
With this as the result.

> 1. 1
  
> 2. 2
  
> 4. 4
  
> 6. fizz
  
> 7. 7
  
> 8. 8
  
> 3. fizz
  
> 5. buzz
  
> 13. 13
  
> 14. 14
  
> 15. fizz buzz
  
> 16. 16
  
> 19. 19
  
> 11. 11
  
> 12. fizz
  
> 17. 17
  
> 18. fizz
  
> 9. fizz
  
> 10. buzz

Which seems a bit more random to me.

That was a fun excercise.

 [1]: http://imranontech.com/2007/01/24/using-fizzbuzz-to-find-developers-who-grok-coding/