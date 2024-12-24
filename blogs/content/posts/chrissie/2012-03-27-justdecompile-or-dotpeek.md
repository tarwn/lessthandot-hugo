---
title: Justdecompile or dotpeek
author: Christiaan Baes (chrissie1)
type: post
date: 2012-03-27T16:13:00+00:00
ID: 1580
excerpt: I needed a decompiler today and decided to try dotpeek and justdecompile.
url: /index.php/desktopdev/mstech/justdecompile-or-dotpeek/
views:
  - 6668
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - decompile
  - dotpeek
  - justdecompile

---
For my previous post I needed to see the IL and that was the time to try some of the new decompilers that are out there. I was thinking [dotpeek][1] by [jetbrains][2] and [justdecompile][3] by [telerik][4].

God knows I love dotpeek. So it was the first I tried. Dotpeek is still early access so far from ready. The install experience is none existing. You download the zipfile, unzip it. And then doubleclick on the exe to start it (if you can&#8217;t find it, it&#8217;s under the d for dotpeek).

So first I created a project and compiled it.

This is the code.

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
I then decompiled with dotpeek.

This was the result.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/decompilers/dotpeek1.png?mtime=1332870812"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/decompilers/dotpeek1.png?mtime=1332870812" width="1034" height="778" /></a>
</div>

So it decompiles just fine. However it only decompiles to C# and not IL. And it felt kind of slow.

I needed IL so I downloaded justdecompile.

The install experience is very nice for justdecompile. Apart from the fact that it tries to install other software on my machine. 

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/decompilers/justdecompile1.png?mtime=1332871045"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/decompilers/justdecompile1.png?mtime=1332871045" width="840" height="641" /></a>
</div>

Look for &#8220;Why not try these&#8221; and uncheck them if you don&#8217;t want them.

And then you have to fill in this.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/decompilers/justdecompile2.png?mtime=1332871279"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/decompilers/justdecompile2.png?mtime=1332871279" width="840" height="641" /></a>
</div>

That&#8217;s really annoying.

But it installed. Now it was time to try it.

This the C# code it produced.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/decompilers/justdecompile3.png?mtime=1332871570"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/decompilers/justdecompile3.png?mtime=1332871570" width="1054" height="799" /></a>
</div>

This is the IL.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/decompilers/justdecompile4.png?mtime=1332871588"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/decompilers/justdecompile4.png?mtime=1332871588" width="1054" height="799" /></a>
</div>

And as an added bonus, this is the Visual basic.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/decompilers/justdecompile5.png?mtime=1332871603"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/decompilers/justdecompile5.png?mtime=1332871603" width="1054" height="799" /></a>
</div>

Don&#8217;t think it was much faster than dotpeek but it had what I needed.

So I think justdecompile had what I needed today. But keeping both around seems like the right thing to do.

Not that I need decompilers all that often.

 [1]: http://www.jetbrains.com/decompiler/
 [2]: http://www.jetbrains.com
 [3]: http://www.telerik.com/products/decompiler.aspx
 [4]: http://www.telerik.com/