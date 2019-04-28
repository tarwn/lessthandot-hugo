---
title: 'VB.Net: Linq and I keep forgetting'
author: Christiaan Baes (chrissie1)
type: post
date: 2008-09-09T05:33:55+00:00
ID: 133
url: /index.php/desktopdev/mstech/vb-net-linq-and-i-keep-forgetting/
views:
  - 4892
categories:
  - Microsoft Technologies
tags:
  - linq
  - vb.net

---
## Problem

I keep forgetting that it needs the System.linq namespace to work. And I keep forgetting that it is not set by default on my projects. I try not to overuse linq but sometimes it is easy. 

So what happened?

I was trying this:

```vbnet
Dim nics As NetworkInterface() = NetworkInterface.GetAllNetworkInterfaces()
Dim result = From e In nics```


## The solution

And it came up with this error and a squiggly line under nics.

> Expression of type &#8216;1-dimensional array of System.Net.NetworkInformation.NetworkInterface&#8217; is not queryable. Make sure you are not missing an assembly reference and/or namespace import for the LINQ provider.

Hmm, not very helpful. What it is trying to say is that I have to add
  
Imports System.Linq to the file and then it works. Why can&#8217;t it just say that instead of that cryptic message? I will be so bold to suggest a rewrite of that error message.

> Expression of type &#8216;1-dimensional array of System.Net.NetworkInformation.NetworkInterface&#8217; is not queryable. Make sure you are not missing an assembly reference and/or namespace import for the linq provider. This means add a reference to System.Core and an Imports System.Linq, have fun.

Or, of course, they could just do it for me, what good is an IDE if it can&#8217;t even solve the obvious mistakes?