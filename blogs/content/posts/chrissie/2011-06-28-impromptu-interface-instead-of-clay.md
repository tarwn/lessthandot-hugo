---
title: impromptu-interface instead of clay for VB.Net
author: Christiaan Baes (chrissie1)
type: post
date: 2011-06-28T16:19:00+00:00
ID: 1234
excerpt: |
  Introduction
  
  A while ago I blogged about Clay for VB.Net and found that it didn't really work for anonymous types in VB.Net. And I even got an explanation why it did not work.
  
  Today I got a comment on my first post saying that impromptu-interface&hellip;
url: /index.php/desktopdev/mstech/impromptu-interface-instead-of-clay/
views:
  - 4777
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - clay
  - impromptu-interface

---
## Introduction

A while ago [I blogged about Clay for VB.Net][1] and found that it [didn&#8217;t really][2] work for anonymous types in VB.Net. And I even got [an explanation why][3] it did not work.

Today [I got a comment][4] on my first post saying that [impromptu-interface][5] would work in VB.Net and since it is on Nuget I would try it.

## Clay

So this code in Clay

```vbnet
Dim c As Object = New ClayFactory
Dim plant = c.Plant(New With {.LatinName = "test"})
Console.WriteLine(plant.LatinName)
Console.ReadLine()```
or this

```vbnet
Dim c As Object = New ClayFactory
Dim plant = c.Plant(LatinName:="test")
Console.WriteLine(plant.LatinName)
Console.ReadLine()```
or this

```vbnet
Dim c As Object = New ClayFactory
Dim plant = c.Plant().LatinName("test")
Console.WriteLine(plant.LatinName)
Console.ReadLine()```
And yes I tried with the newest version of clay and it still doesn&#8217;t work.

## impromptu-interface 

So I tried this with impromptu-interface and that just worked. 

```
Dim c As Object = Builder.[New]()
        Dim plant = c.Plant(New With {.LatinName = "test"})
        Console.WriteLine(plant.LatinName)
        Dim d As Object = Builder.[New]()
        Dim plant2 = d.Plant(LatinName:="test")
        Console.WriteLine(plant2.LatinName)
        Dim e As Object = Builder.[New]()
        Dim plant3 = e.Plant().LatinName("test")
        Console.WriteLine(plant3.LatinName)
        Console.ReadLine()```
So go check it out.

## Conclusion

Great work from these guys to make that work.

 [1]: /index.php/DesktopDev/MSTech/using-clay-in-vb-net
 [2]: /index.php/DesktopDev/MSTech/the-mystery-of-clay-and
 [3]: /index.php/DesktopDev/MSTech/the-mystery-of-clay-and-1
 [4]: /index.php/DesktopDev/MSTech/using-clay-in-vb-net#c8526
 [5]: http://code.google.com/p/impromptu-interface/