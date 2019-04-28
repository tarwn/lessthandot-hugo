---
title: 'The Like keyword in VB.Net and the equivalent in C#'
author: Christiaan Baes (chrissie1)
type: post
date: 2011-08-10T16:49:00+00:00
ID: 1296
excerpt: The Like keyword in VB.Net is not very well known, I just found out today ;-). Well I have probably seen it before but I keep forgetting it is there. But writing about it helps me to remember.
url: /index.php/desktopdev/mstech/the-like-keyword-in-vb/
views:
  - 6669
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - 'c#'
  - like
  - vb.net

---
## Introduction

The Like keyword in VB.Net is not very well known, I just found out today ;-). Well I have probably seen it before but I keep forgetting it is there. But writing about it helps me to remember.

## VB.Net

The Like keyword in V.Net works like the Like keyword in SQL. The following passing tests prove this.

```vbnet
Imports NUnit.Framework

&lt;TestFixture()&gt;
Public Class LikeTests
    &lt;Test()&gt;
    Public Sub IfLikeReturnsTrueWhenUsingOneCharacterWildcard()
        Assert.IsTrue("st" Like "?t")
    End Sub

    &lt;Test()&gt;
    Public Sub IfLikeReturnsFalseWhenUsingOneCharacterWildcardFindsNothing()
        Assert.IsFalse("test" Like "?t")
    End Sub

    &lt;Test()&gt;
    Public Sub IfLikeReturnsTrueWhenUsingMultiCharacterWildcardBeginswith()
        Assert.IsTrue("test" Like "te*")
    End Sub

    &lt;Test()&gt;
    Public Sub IfLikeReturnsTrueWhenUsingMultiCharacterWildcardEndswith()
        Assert.IsTrue("test" Like "*st")
    End Sub

    &lt;Test()&gt;
    Public Sub IfLikeReturnsTrueWhenUsingMultiCharacterWildcardInBetween()
        Assert.IsTrue("test" Like "*st*")
    End Sub

    &lt;Test()&gt;
    Public Sub IfLikeReturnsFalseWhenUsingMultiCharacterWildcardAndNothingIsFound()
        Assert.IsFalse("test" Like "*a*")
    End Sub
End Class```
What the above tell us is that we can use wildcards (? for one character and * for multi character). 

You can find [everything about Like on MSDN][1].

## C#

There is no equivalent of this in C#. 😉

<span class="MT_red">Edit</span>

But you can reference the VisualBasic Assembly if you want. And then write something ugly like this.

```csharp
using System;
using Microsoft.VisualBasic;
using Microsoft.VisualBasic.CompilerServices;

namespace ConsoleApplication2
{
    class Program
    {
       static void Main(string[] args)
       {
           Console.WriteLine(LikeOperator.LikeString("Test", "*t", CompareMethod.Text));
           Console.ReadLine();
       }
    }
}```
Perhaps there is even a better way to do this.

## Conclusion

Something you should now exists because it can be very handy someday. At least you can avoid using regexp for the above cases. So this falls under the category, good to know.

 [1]: http://msdn.microsoft.com/en-us/library/swf8kaxw.aspx