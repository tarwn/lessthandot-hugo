---
title: 'Another decompiler: ILSpy.'
author: Christiaan Baes (chrissie1)
type: post
date: 2012-03-27T17:38:00+00:00
ID: 1581
excerpt: Another decompiler, this one is called ILSpy.
url: /index.php/desktopdev/mstech/another-decompiler-ilspy/
views:
  - 2918
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - decompiler
  - ilspy
  - vb.net

---
This one is open source (didn&#8217;t really check if the others were) and also free.

And the little one is called [ILSpy][1].

Like with the [previous post about justdecompile and dotpeek][2] I also decompiled the following code.

```vbnet
Module Module1
 
    Sub Main()
        Dim array1() As Integer
        Dim array2(-1) As Integer
        Dim array3(0) As Integer
        Dim array4(1) As Integer
        Dim array5() As Integer = {}
        Dim array6() = New Integer() {}
 
        ReDim array1(-1)
 
        Console.WriteLine(array1.Count)
        Console.WriteLine(array2.Count)
        Console.WriteLine(array3.Count)
        Console.WriteLine(array4.Count)
        Console.WriteLine(array5.Count)
        Console.WriteLine(array6.Count)
 
        Console.ReadLine()
    End Sub
 
End Module```
And these are the results.

For C3.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/decompilers/ILSpy1.png?mtime=1332876766"><img alt="" src="/wp-content/uploads/users/chrissie1/decompilers/ILSpy1.png?mtime=1332876766" width="963" height="682" /></a>
</div>

For IL.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/decompilers/ILSpy2.png?mtime=1332876779"><img alt="" src="/wp-content/uploads/users/chrissie1/decompilers/ILSpy2.png?mtime=1332876779" width="963" height="682" /></a>
</div>

Fro VB.Net.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/decompilers/ILSpy3.png?mtime=1332876792"><img alt="" src="/wp-content/uploads/users/chrissie1/decompilers/ILSpy3.png?mtime=1332876792" width="963" height="682" /></a>
</div>

This one seemed slightly faster.

I am not making any judgment and I guess that when you really need a decompiler some features are more important than others depending on your use case. My use case was to see the IL which only dotpeek did not provide.

 [1]: http://wiki.sharpdevelop.net/ILSpy.ashx
 [2]: /index.php/DesktopDev/MSTech/justdecompile-or-dotpeek