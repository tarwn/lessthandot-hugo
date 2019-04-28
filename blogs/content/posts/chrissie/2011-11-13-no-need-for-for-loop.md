---
title: No need for a “for loop” when you have linq
author: Christiaan Baes (chrissie1)
type: post
date: 2011-11-13T07:25:00+00:00
ID: 1382
excerpt: |
  From time to time I answer a few questions on Stackoverflow, mostly because you can learn a lot from someone else's problems and it makes you think.
  This time the problem was simple and it seems like most people revert to a for loop loop to solve it. While using linq is so much shorter easier to read.
url: /index.php/desktopdev/mstech/no-need-for-for-loop/
views:
  - 2180
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - linq
  - vb.net

---
## Introduction

Like I said in a [previous post of mine][1].

From time to time I answer a few questions on Stackoverflow, mostly because you can learn a lot from someone else&#8217;s problems and it makes you think.

[
  
<img src="http://stackoverflow.com/users/flair/2936.png" width="208" height="58" alt="profile for chrissie1 at Stack Overflow, Q&A for professional and enthusiast programmers" title="profile for chrissie1 at Stack Overflow, Q&A for professional and enthusiast programmers" />
  
][2] 

Thinking is something you need to practice every day.

This time the problem was simple and it seems like most people revert to a for loop loop to solve it. While using linq is so much shorter easier to read.

## The question

The question was how to &#8220;[Find the first non-repeating character in a string in VB.NET][3]&#8221;

First of all we need some more test data.

```vbnet
Imports System.Linq

Module Module1

    Sub Main()
        Console.WriteLine(FirstCharacterToNotRepeat(Nothing))
        Console.WriteLine(FirstCharacterToNotRepeat(""))
        Console.WriteLine(FirstCharacterToNotRepeat("BBEEXEE"))
        Console.WriteLine(FirstCharacterToNotRepeat("BBEEEE"))
        Console.WriteLine(FirstCharacterToNotRepeat("XBBEEEE"))
        Console.WriteLine(FirstCharacterToNotRepeat("BBEEEEX"))
        Console.WriteLine(FirstCharacterToNotRepeat("BBEEXEEACEED"))
        Console.ReadLine()
    End Sub

    Private Function FirstCharacterToNotRepeat(ByVal input As String) As String
        return String.Empty
    End Function
End Module
```
And now we need some answers.

## The for loop

Here is my solution using a for loop. 

```vbnet
Private Function FirstCharacterToNotRepeat(ByVal input As String) As String
        If String.IsNullOrEmpty(input) Then Return String.Empty
        Dim charlist As New Dictionary(Of Char, Int32)
        For Each c In input
            If charlist.Keys.Contains(c) Then
                charlist(c) = charlist(c) + 1
            Else
                charlist.Add(c, 1)
            End If
        Next
        For Each c In charlist
            If c.Value = 1 Then Return c.Key
        Next
        Return String.Empty
End Function```
There are actually 2 for loops in there, one to put every character in the dictionary and count the occurrences and one to get out the first element that has only 1 occurrence. 

## The linq solution

And here is my linq solution.

```vbnet
Private Function FirstCharacterToNotRepeat(ByVal input As String) As String
        If String.IsNullOrEmpty(input) Then Return String.Empty
        Return (input.GroupBy(Function(x) x).Where(Function(x) x.Count = 1).Select(Function(x) x.First))(0)
    End Function```
First I do a group by on all characters. Then I do a where on the count of the result of the group by, where I only want the occurrences with a count of 1. And then I just select the first occurrence with a count of 1.

Simple.

## Conclusion

Knowing and using linq can make your code so much shorter and easier for you to write and read.

 [1]: /index.php/DesktopDev/MSTech/join-and-skipwhile
 [2]: http://stackoverflow.com/users/2936/chrissie1
 [3]: http://stackoverflow.com/questions/8108714/find-the-first-non-repeating-character-in-a-string-in-vb-net