---
title: 'In VB11 the namespace difference between VB and C# will be … smaller.'
author: Christiaan Baes (chrissie1)
type: post
date: 2011-09-28T06:09:00+00:00
ID: 1330
excerpt: |
  In a previous post I wrote about the difference in namespaces between VB.Net and C#. 
  
  On a project level C# uses a default namespace setting which means you can just overwrite it at the class level.
  
   
  
  So if you create a new class you will get s&hellip;
url: /index.php/desktopdev/mstech/in-vb11-the-namespace-difference/
views:
  - 2053
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - global
  - namespace
  - vb.net
  - vb11

---
Yesterday there was [this announcement from the VB-team][1].

In [a previous post][2] I wrote about the difference in namespaces between VB.Net and C#. 

On a project level C# uses a default namespace setting which means you can just overwrite it at the class level.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/namespace.jpg" alt="" title="" width="744" height="208" />
</div>

So if you create a new class you will get something like this.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
 
namespace C_Class_Library_1.NewFolder1
{
    class Class1
    {
    }
}```
But if you so desire you can change that. To this.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
 
namespace NewFolder1
{
    class Class1
    {
    }
}```
Which means that your class is in a different namespace than all the other classes in your project. 

In VB.Net this was never possible, because they use a root namespace at the project level. Which means that all classes in your project will be under that namespace.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/namespace2.jpg" alt="" title="" width="716" height="149" />
</div>

You can of course cheat and not set a root namespace and then set a namespace on each and every file/class.

But [in VB11 you will no longer have to do this][1]. 

You can now override the root namespace setting by using the Global keyword.

So if I do this.

```vbnet
Namespace Global.NewNamespace
    Public Class Class1
 
 
    End Class
End Namespace

Public Class Class1

End Class```
So I can now call the above classes like so.

```vbnet
Dim _class1 As New NewNamespace.Class1
Dim _class1_1 As New VB_Console.Class1```
Perhaps not the coolest feature ever but it is new. But to be honest we would have prefered having project Roslyn in this version.

 [1]: http://blogs.msdn.com/b/vbteam/archive/2011/09/27/announcement-namespace-global.aspx
 [2]: /index.php/DesktopDev/MSTech/vb-net-and-c-the-difference-in-oo-syntax-4