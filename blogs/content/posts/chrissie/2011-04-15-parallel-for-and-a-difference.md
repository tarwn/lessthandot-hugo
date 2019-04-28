---
title: 'Parallel.For and a difference between C# and VB.Net'
author: Christiaan Baes (chrissie1)
type: post
date: 2011-04-15T08:05:00+00:00
ID: 1116
excerpt: |
  While writing my last 2 blogposts I noticed that the Parallel.For was kinda better in C# than the VB.Net version.
  
  This is what I had in VB.Net.
  
  
  Parallel.For(0, 5, Sub(b)
    CheckOnline(b)
  End Sub)
  
  Which would be this in C#
  
  Parallel.For(0,&hellip;
url: /index.php/desktopdev/mstech/parallel-for-and-a-difference/
views:
  - 3396
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - 'c#'
  - parallel.for
  - vb.net

---
While writing my last 2 blogposts I noticed that the Parallel.For was kinda better in [C#][1] than the [VB.Net][2] version.

This is what I had in VB.Net. 

<span class="MT_red">Edit</span>

```vbnet
Parallel.For(0, 5, Sub(b)
  CheckOnline(b)
End Sub)```
Which would be this in C#

```csharp
Parallel.For(0, 5, b =&gt; {CheckOnline(b)});```
or the slightly shorter versions.

```vbnet
Parallel.For(0, 5, Sub(b) CheckOnline(b))```
Which would be this in C#

```csharp
Parallel.For(0, 5, b =&gt; CheckOnline(b));```
<span class="MT_red">End Edit.</span>

or the even shorter version in C# first.

```csharp
Parallel.For(0, 5, CheckOnline);```
Apparently it is able to infer the parameter to send in C# (see no more b just the methodname. Resharper told me that. So I searched for a way to do this in VB.Net and here it is.

```vbnet
Parallel.For(0, 5, AddressOf CheckOnline)```
A little more verbose but just a little. 

I learn a little every day.

 [1]: /index.php/DesktopDev/MSTech/multithreaded-ping-shown-in-a
 [2]: /index.php/DesktopDev/MSTech/multithreading-pings-and-showing-them