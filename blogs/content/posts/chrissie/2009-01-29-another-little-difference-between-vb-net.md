---
title: 'Another little difference between VB.Net and C#'
author: Christiaan Baes (chrissie1)
type: post
date: 2009-01-29T06:48:11+00:00
ID: 302
url: /index.php/desktopdev/mstech/another-little-difference-between-vb-net/
views:
  - 5721
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - byref
  - byval
  - 'c#'
  - vb.net

---
I use C# to test my VB.Net assemblies. So today I got this little errormessage when doing this.

Here is the VB.Net method.

```vbnet
''' &lt;summary&gt;
Public Sub SetFiberGroup(ByRef FiberGroup As FiberGroup)
  _FiberGroup = FiberGroup
End Sub```
I don&#8217;t use ByRef all that often but this time it was needed.

And normally you would call this like this in VB.Net.

```vbnet
dim _FiberGroup as new FiberGroup
_obj.SetFiberGroup(_FiberGroup)```
Or something like that. No different then setting a ByVal parameter.

So I would expect C# to act the same. But it doesn&#8217;t.

C# needs the ref keyword. like this

```vbnet
var _FiberGroup = new FiberGroup();
_obj.SetFiberGroup(ref _FiberGroup);```
If you don&#8217;t use the ref keyword then you need to get this little gem of an error.

> Argument is &#8216;value&#8217; while parameter is declared as &#8216;ref&#8217;

So the solution is simple and logical. But I just wrote this in case anybody else bumps into this because Google doesn&#8217;t seem to find that specific errormessage. Perhaps Yahoo will or Microsoft search but who uses those anymore.