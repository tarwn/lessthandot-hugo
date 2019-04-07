---
title: VB6 Remove date part of DateTime
author: George Mastros (gmmastros)
type: post
date: 2009-04-01T12:51:55+00:00
url: /index.php/desktopdev/mstech/vb56/vb6-remove-date-part-of-datetime/
views:
  - 10800
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft Access
  - VBA for Microsoft Office Products
  - VBScript
  - Visual Basic 5 and 6

---
Have you ever needed to remove the time part of a date variable, or remove the date part of a date variable? I recently had a requirement to do this, and my first reaction was to use the format function because it is very flexible and quite simple to use. Unfortunately, it does&#8217;t perform very well.

First, the format method for removing the time part.

<pre><span class="MT_blue">Debug.Print Format(Now, "Short Date")
4/1/2009

Debug.Print Format(Now, "Short Time")
09:16</span> </pre>

Seems simple enough to remove the date and/or time portion of a date variable, but is it the best way? If you only want to display it, it probably is, but if you need to use the values for calculations, it is not the best way to remove the date or time portions.

Instead, you can use the Int function.

The Int function performs a simple truncate on a number. Note: this does not do rounding. Also significant is that Int will return whatever data is sent to it. If the parameter is a double, int will return a double. If the parameter is a single, Int returns a single, if the parameter is a date, Int returns a date.

<pre><span class="MT_blue">Debug.Print Int(Now)
4/1/2009 

Debug.Print Now - Int(Now)
0.390729166669189 </span> </pre>

Surprised to see a number instead of a time? Me too. But&#8230; in VB6, when you subtract dates, the resulting data type is a double.

<pre><span class="MT_blue">Debug.Print TypeName(Now - Int(Now))
Double</span> </pre>

However, this double can be converted back to a Date variable type like this.

<pre><span class="MT_blue">Debug.Print cDate(Now - Int(Now))
9:25:40 AM</span> </pre>

Clearly, there are 2 different methods for separating the date from the time in a Date variable. Which is better? They both return the correct value, so now it&#8217;s up to performance.

I tested the performance with this code:

<pre>Option Explicit

Private Sub Command1_Click()

    Dim i As Long
    Dim dteTemp As Date
    Dim Start As Single
    
    Start = Timer
    For i = 1 To 1000000
        dteTemp = RemoveDate(Now)
    Next
    Call MsgBox(Format(Timer - Start, "0.00") & " micro-seconds")
    
    Start = Timer
    For i = 1 To 1000000
        dteTemp = RemoveDateWithFormat(Now)
    Next
    Call MsgBox(Format(Timer - Start, "0.00") & " micro-seconds")
    
End Sub

Private Function RemoveDateWithFormat(ByVal DateTime As Date) As Date
    
    RemoveDateWithFormat = Format(DateTime, "h:mm")

End Function

Private Function RemoveDate(ByVal DateTime As Date) As Date
    
    RemoveDate = DateTime - Int(DateTime)
    
End Function</pre>

The Int method runs in 0.40 micro-seconds and the Format method takes 3 micro-seconds. The Int method is approximately 7 times faster.