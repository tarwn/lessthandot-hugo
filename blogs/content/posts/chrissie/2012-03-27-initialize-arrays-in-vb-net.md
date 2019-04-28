---
title: Initialize arrays in VB.Net
author: Christiaan Baes (chrissie1)
type: post
date: 2012-03-27T04:57:00+00:00
ID: 1577
excerpt: Arrays in VB.Net are 0 based and people seem to forget that.
url: /index.php/desktopdev/mstech/initialize-arrays-in-vb-net/
views:
  - 7400
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - array
  - vb.net

---
This post was inspired by [this forumthread][1] on [VBIB][2] (dutch).

Some people seem to forget that arrays in VB.Net are 0-based. Meaning that this.

```vbnet
Dim array1(0) As Integer
Console.WriteLine(array1.Count)```
This instantiates the array with one element in it. But there is nothing stopping you from creating an array with 0 elements in it. Appart from the fact that it seems a bit unnatural.

```vbnet
Dim array1(-1) As Integer
Console.WriteLine(array1.Count)```
And why would you do that? Why not just do.

```vbnet
Dim array1() As Integer
Console.WriteLine(array1.Count)```
Because the above will give you a NullReferenceException, that&#8217;s why.

So doing the unnatural is perhaps the way to go if you really need an array that has no predefined size.

 [1]: http://www.vbib.be/index.php?/topic/10807-module-array-variabele/page__pid__59243#entry59243
 [2]: http://www.vbib.be/