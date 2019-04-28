---
title: The new features in VB.Net for the .net framework 4.5 or VB11, I’m not impressed.
author: Christiaan Baes (chrissie1)
type: post
date: 2011-10-08T06:13:00+00:00
ID: 1345
excerpt: |
  Introduction
  
  Their are a number of new features for the next version of VB.Net. I'm guessing that next version will be called VB11 but it could be VB10.5, who knows. 
  
  The least you can say is that the number of new features is not that impressive.&hellip;
url: /index.php/desktopdev/mstech/the-new-features-in-vb/
views:
  - 2753
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - .net 4.5
  - vb.net
  - vb11

---
## Introduction

Their are a number of new features for the next version of VB.Net. I&#8217;m guessing that next version will be called VB11 but it could be VB10.5, who knows. 

The least you can say is that the number of new features is not that impressive. According to [the official MSDN website][1] 

  * Async Feature
  * Call Hierarchy
  * Global Keyword in Namespace Statements
  * Iterators

## Async

Async/Await has been around in the form of a CTP for a while, it was a separate project up till now. I think this might be big but I&#8217;m sure that people who don&#8217;t understand multithreading won&#8217;t understand this either. Since you can use it with the .Net framework 4 I would not call this a &#8220;new&#8221; feature of .net framework 4.5.
  
I have blogged about Async before.

  * [Multithreading pings now with Visual studio Async CTP SP1 refresh][2]
  * [Visual Studio Async CTP videos][3]

## Call Hierarchy

> Call Hierarchy enables you to navigate through your code by displaying the following:
> 
> All calls to and from a selected method, property, or constructor.
> 
> All implementations of an interface member.
> 
> All overrides of a virtual or abstract member. 

Why am I not that overwhelmed by this new feature? I already had all this and more with [Resharper][4]. Let&#8217;s move on nothing to see here, not for me anyway.

## The namespace thing

I blogged about this a short while ago. &#8220;[In VB11 the namespace difference between VB and C# will be &#8230; smaller.][5]&#8220;. This one solves a problem that never really bothered me. But I guess some people will use this a couple of times a year.

## Iterators

Now this is a feature I have been waiting for. Finally something I think is really new and useful. I wanted more of this. That&#8217;s also why I kept it for last.

Now what is it. In essence they added the yield keyword to VB.Net. The yield keyword has been around in C# since VS2005. 

You can see what it does on [the MSDN page][6]. 

And if you switch between the C# code and the VB.Net code you will see something odd.

I&#8217;ll show the relevant lines below.

```csharp
public static System.Collections.IEnumerable SomeNumbers()```
```vbnet
Private Iterator Function SomeNumbers() As System.Collections.IEnumerable```
Never mind the difference in public and private or static and no shared keyword because you are in a module things. 

It&#8217;s the Iterator keyword you need in VB.Net to get the yield to work. So when you use the yield keyword in a method you have to make that method an Iterator. Probably not the most elegant way of doing things but not to bad either.

And you will want to pay special attention to this section.

> Anonymous Methods in Visual Basic
> 
> In Visual Basic <span class="MT_red">(but not in C#)</span>, an anonymous function can be an iterator function. The following example illustrates this.

```vbnet
Dim iterateSequence = Iterator Function() _
                      As IEnumerable(Of Integer)
                          Yield 1
                          Yield 2
                      End Function```
As we all know the &#8220;but not in C#&#8221; bit, can come back and bite you in the future. not sure what will happen if you pass that function to a C# lib. I might need to try that. It might just work.

## Conclusion

There is only one feature in that list that seems to be worth the upgrade. The biggest question of course is if they have succeeded in solving some of the incompatibilities with C#. I will write about that later.

 [1]: http://msdn.microsoft.com/en-us/library/we86c8x2%28v=vs.110%29.aspx
 [2]: /index.php/DesktopDev/MSTech/multithreading-pings-now-with-visuad?highlight=async&sentence=
 [3]: /index.php/DesktopDev/MSTech/visual-studio-async-ctp-videos?highlight=async&sentence=
 [4]: http://www.jetbrains.com/resharper/
 [5]: /index.php/All/?p=1421
 [6]: http://msdn.microsoft.com/en-us/library/dscyy5s0%28v=vs.110%29.aspx