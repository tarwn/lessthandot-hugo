---
title: 'VB.Net: Some fun with enums'
author: Christiaan Baes (chrissie1)
type: post
date: 2008-09-12T07:41:02+00:00
ID: 137
url: /index.php/desktopdev/mstech/vb-net-some-fun-with-enums/
views:
  - 5232
categories:
  - Microsoft Technologies
tags:
  - bitwise operators
  - vb.net

---
Enums can be used in some very interesting ways. Just look at the anchor property on your windowsform that uses enums and you can combine them.

But can you also combine different enums?

Yes, you can, as long as you set the value to an integer.

Look at this example:

```vbnet
Module Module1

    Sub Main()
        CheckColor(Redcolors.Pink)
        CheckColor(Redcolors.Red)
        CheckColor(Bluecolors.Blue)
        CheckColor(Bluecolors.DarkBlue)
        CheckColor(Bluecolors.Blue Or Redcolors.Red)
        CheckColor(Purplecolors.Purple)
        Console.ReadLine()
    End Sub

    Private Sub CheckColor(ByVal color As Integer)
        If Not color = Purplecolors.Purple Then
            If Not color = Bluecolors.Blue Then
                Console.WriteLine("this is not blue or purple")
            Else
                Console.WriteLine("this is blue")
            End If
        Else
            Console.WriteLine("this is purple")
        End If
    End Sub


    &lt;Flags()&gt; _
    Public Enum Redcolors As Integer
        Red = 1
        Pink = 2
    End Enum

    &lt;Flags()&gt; _
    Public Enum Bluecolors As Integer
        Blue = 4
        DarkBlue = 8
    End Enum

    &lt;Flags()&gt; _
    Public Enum Purplecolors As Integer
        Purple = 5
    End Enum
End Module
```
With this as the outcome:

> this is not blue or purple
  
> this is not blue or purple
  
> this is blue
  
> this is not blue or purple
  
> this is purple
  
> this is purple

The interesting part is **CheckColor(Bluecolors.Blue Or Redcolors.Red)** Logic says that you should use an AND to make purple and not an OR but when using a + operator, it does give the correct result, the same as OR.
  
What it does is bitwise calculations, so the OR is not adding the integer but is Xoring the bit.

Blue is 4, which looks like this in binary:

0100

Red is 1, which looks like this in binary:

0001

If we AND them we get this:

0100
  
0001
  
&#8212;-
  
0000 which is 0

If we OR them we get this:

0100
  
0001
  
&#8212;-
  
0101 which is 5

I don&#8217;t know when this is useful 😉 but it can be sometimes, I guess 😉

You can find more information about bitwise operations on [wikipedia][1].

BTW in this example doing Xor or Or wouldn&#8217;t have made a difference.

 [1]: http://en.wikipedia.org/wiki/Bitwise_operation