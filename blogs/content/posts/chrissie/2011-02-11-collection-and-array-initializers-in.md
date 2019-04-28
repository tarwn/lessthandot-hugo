---
title: 'Collection and array initializers in VB.Net and the difference with C#'
author: Christiaan Baes (chrissie1)
type: post
date: 2011-02-11T08:26:00+00:00
ID: 1041
excerpt: |
  Ok I admit VB.Net is a weird language and one wonders where the designers got their inspiration from. 
  
  Let's see how we can initialize an array while letting the compiler infer the type.
  
  In C# that would be.
  
  var stringarray = new[] { "test", "t&hellip;
url: /index.php/desktopdev/mstech/collection-and-array-initializers-in/
views:
  - 2494
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - array initializers
  - 'c#'
  - collection initializers
  - vb.net

---
Ok I admit VB.Net is a weird language and one wonders where the designers got their inspiration from. 

Let&#8217;s see how we can initialize an array while letting the compiler infer the type.

In C# that would be.

```csharp
var stringarray = new[] { "test", "test" };```
In VB.Net this is.

```vbnet
Dim stringarray = {"test", "test"}```
Which might be slightly better and shorter than the C# version.
  
Now lets see the syntax for when we want to specify the type.

In C#.

```csharp
var stringarray = new string[] {"test", "test"};```
And in VB.Net that will be.

```vbnet
Dim stringarray = New String() {"test", "test"}```
Both are ok.

Now lets look at the collection initializers like list.

In C#.

```csharp
var stringlist = new List&lt;string&gt; {"test", "test"};```
In VB.Net.

```vbnet
Dim stringlist = New List(Of String) From {"test", "test"}```
The thing that strikes me as odd here is that someone decided they needed the From keyword here to make it work. 

So to recap.

C#

```csharp
var stringarray = new[] { "test", "test" };
var stringarray = new string[] {"test", "test"};
var stringlist = new List&lt;string&gt; {"test", "test"};```
VB.Net

```vbnet
Dim stringarray = {"test", "test"}
Dim stringarray = New String() {"test", "test"}
Dim stringlist = New List(Of String) From {"test", "test"}```
I think the C# version is much more consistent than the VB version so it would be nice if someone fixed that in the next version, thank you in advance ;-).