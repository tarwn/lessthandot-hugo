---
title: Join and SkipWhile
author: Christiaan Baes (chrissie1)
type: post
date: 2011-09-19T09:37:00+00:00
ID: 1322
excerpt: |
  From time to time I answer a few questions on Stackoverflow, mostly because you can learn a lot from someone else's problems and it makes you think.
  By answering those question I found a use for two functions I rarely use. Namely the SkipWhile extension method and String.Join.
url: /index.php/desktopdev/mstech/join-and-skipwhile/
views:
  - 4580
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - join
  - skipwhile
  - vb.net

---
## Introduction

From time to time I answer a few questions on Stackoverflow, mostly because you can learn a lot from someone else&#8217;s problems and it makes you think.

[
  
<img src="http://stackoverflow.com/users/flair/2936.png" width="208" height="58" alt="profile for chrissie1 at Stack Overflow, Q&A for professional and enthusiast programmers" title="profile for chrissie1 at Stack Overflow, Q&A for professional and enthusiast programmers" />
  
][1] 

Thinking is something you need to practice every day.

By answering those question I found a use for two functions I rarely use. Namely the SkipWhile extension method and String.Join.

## SkipWhile

The first question was &#8220;[How to I count the number of zeros in string until i come across a non zero vb.net][2]&#8220;.

> IF the user enters a 00001 the count would be 4
  
> If the user enters a 0811 the count would be 1

The first thing that springs to mind to solve this is to use a for-loop. And you can see that in the accepted answer.

But there is also a oneliner that uses SkipWhile.

Just try this and you will see the result is as asked.

```vbnet
Module Module1

    Sub Main()
        Console.WriteLine(GetLeadingZeros("00001"))
        Console.WriteLine(GetLeadingZeros("0889"))
        Console.WriteLine(GetLeadingZeros("1"))
        Console.WriteLine(GetLeadingZeros("00101"))
        Console.WriteLine(GetLeadingZeros("11111"))
        Console.WriteLine(GetLeadingZeros("10001"))
        Console.ReadLine()
    End Sub

    Public Function GetLeadingZeros(ByVal input As String) As String
        Return input.Substring(0, input.IndexOf(input.SkipWhile(Function(e) e = "0")(0)))
    End Function
End Module
```
You can read all about [SkipWhile on MSDN][3] so no need for me to explain it.

## Join

The second question had this title &#8220;[Problem in updating sql servere database dynamically in vb.net.][4]&#8221;

> I&#8217;m trying to generate a query dynamically for taking values from textboxes on the update bookings form and update only those values in database whose value has been entered by the user

My first solution was to also use a for each.

```vbnet
Dim str As String  
str = "UPDATE Bookings SET "
Dim comma As string = ""
For Each x As Control In Me.Controls
  If x.GetType Is GetType(TextBox) Then
    If x.Tag = 1 Then
      str &= comma & x.Name & " = @" & x.Name
      comma = ","
    End If
  End If
Next
```
But I found the oneliner to be more elegant.

```vbnet
Dim str = "UPDATE Bookings SET " & String.Join(",", (From _E In Controls.OfType(Of Control)() Where _E.GetType() Is GetType(TextBox) AndAlso _E.Tag = "1" Select _E.Name).ToList())```
## Conclusion

Sometimes it is handy to know all these functions and sometimes you even find a use for some of them. But I am surer there are still hundreds if not thousands of things in the .Net framework I never used in these past 9 years.

 [1]: http://stackoverflow.com/users/2936/chrissie1
 [2]: http://stackoverflow.com/questions/7462019/how-to-i-count-the-number-of-zeros-in-string-until-i-come-across-a-non-zero-vb-ne/7462269#7462269
 [3]: http://msdn.microsoft.com/en-us/library/bb549288.aspx
 [4]: http://stackoverflow.com/questions/7469243/problem-in-updating-sql-servere-database-dynamically-in-vb-net/7469636#7469636