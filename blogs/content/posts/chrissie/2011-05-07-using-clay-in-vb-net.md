---
title: Using clay in VB.Net or how to be dynamic
author: Christiaan Baes (chrissie1)
type: post
date: 2011-05-07T06:04:00+00:00
ID: 1161
excerpt: |
  Introduction
  
  Today Scott Hanselman did a post about NuGet Package of the Week #6 - Dynamic, Malleable, Enjoyable Expando Objects with Clay and allthough Scott is slightly awesome, he si not awesome enough to add VB.Net samples. So I contacted him and&hellip;
url: /index.php/desktopdev/mstech/using-clay-in-vb-net/
views:
  - 6790
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - clay
  - dynamic
  - vb.net

---
## Introduction

Today [Scott Hanselman][1] did a post about [NuGet Package of the Week #6 &#8211; Dynamic, Malleable, Enjoyable Expando Objects with Clay][2] and allthough Scott is slightly awesome, he is not awesome enough to add VB.Net samples. So I contacted him and asked if I could do that for him. And here I am.

## Clay

I will refer you to Scott&#8217;s post on how to get Clay, it only takes seconds with Nuget. 

First thing to note

> <span class="MT_red">Clay does NOT work with the client profile. I get bitten way to often with this to be funny anymore.</span> 

I don&#8217;t like the client profile.

If you go around the internet and Google for dynamic and VB.Net you will find that you have to turn option strict off. And it is all true.

You also do not need the dynamic keyword since VB has been dynamic for years and hence superior to C# ;-).

What you will see is that ClayFactory does not get inferred correctly. You will have to explicitly tell VB that you need ClayFactory to be of type Object.

So this will not work.

```vbnet
Dim c = New ClayFactory
Dim plant = c.Plant()
plant.LatinName = "test"
Console.WriteLine(plant.LatinName)
```
It will tell you that the Plant method on c does not exist.

But this will work.

```vbnet
Dim c As Object = New ClayFactory
Dim plant = c.Plant()
plant.LatinName = "test"
Console.WriteLine(plant.LatinName)
```
However, if you turn Option Infer Off you do not have to explicitly make it an Object. Crazy stuff, I know.

And so we continue.

With indexers.

```vbnet
Dim c As Object = New ClayFactory
Dim plant = c.Plant()
plant("LatinName") = "test"
Console.WriteLine(plant.LatinName)
Console.ReadLine()```
I could not get it to work with.

With Anonymous object.

```vbnet
Dim c As Object = New ClayFactory
Dim plant = c.Plant(New With {.LatinName = "test"})
Console.WriteLine(plant.LatinName)
Console.ReadLine()```
With named parameters.

```vbnet
Dim c As Object = New ClayFactory
Dim plant = c.Plant(LatinName:="test")
Console.WriteLine(plant.LatinName)
Console.ReadLine()```
Or chaining.

```vbnet
Dim c As Object = New ClayFactory
Dim plant = c.Plant().LatinName("test")
Console.WriteLine(plant.LatinName)
Console.ReadLine()```
All of the above is valid VB syntax. But I got an exception.

> Cannot close over byref parameter &#8216;$arg1&#8217; referenced in lambda &#8221;

I will have to try further and see why that is.

## Conclusion

So Clay and dynamic work in VB, allthough with a few quirks. I also think that it was kinda slow, static objects are faster.

 [1]: http://www.hanselman.com/blog
 [2]: http://www.hanselman.com/blog/NuGetPackageOfTheWeek6DynamicMalleableEnjoyableExpandoObjectsWithClay.aspx