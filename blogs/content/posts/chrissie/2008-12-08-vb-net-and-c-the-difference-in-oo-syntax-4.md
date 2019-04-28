---
title: 'VB.Net and C# – the difference in OO syntax part 4'
author: Christiaan Baes (chrissie1)
type: post
date: 2008-12-08T09:16:46+00:00
ID: 239
excerpt: |
  Part 3
  
  Like I said in my previous post, I am now going to talk about the difference in namespace and variable declaration.
  
  
    C# and VB.NET Comparison Cheat Sheet
  
  
  
  
  Namespace or namespace
  
  The only real difference between C# and VB.Net i&hellip;
url: /index.php/desktopdev/mstech/vb-net-and-c-the-difference-in-oo-syntax-4/
views:
  - 4433
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - 'c#'
  - namespace
  - variable declarations
  - vb.net

---
[Part 3][1]

Like I said in my previous post, I am now going to talk about the difference in namespace and variable declaration.

  * [C# and VB.NET Comparison Cheat Sheet][2]

## Namespace or namespace

The only real difference between C# and VB.Net is the capitalization. VB.Net uses Namespace, while C# uses namespace.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace C_Class_Library_1
{
    public class Class1
    {
    }
}
```
By default C# will also always add the default namespace and the folderhierarchy, for example:

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
The default namespace can be found in the properties of your project, in the application tab.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/namespace.jpg" alt="" title="" width="744" height="208" />
</div>

When you leave out the namespace, you will find your classes in the default namespace.
  
The default namespace usually uses the same name as the project name but you can change it if you really want to.

In VB.Net, neither the namespace nor the folder hierarchy is added when you create a class. But you can have this behaviour when you add [Resharper][3] to your toolbox. Which you really should do.

When you do not provide a namespace, the class will go into the Root Namespace.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/namespace2.jpg" alt="" title="" width="716" height="149" />
</div>

This is where you have a big difference between C# and VB.Net. 

This with the default namespace set to C\_Class\_Library_1

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace C_Class_Library_1
{
    public class Class1
    {
    }
}
```
and this with the root namespace set to VB\_Class\_Library_1

```vbnet
Namespace VB_Class_Library_1
    Public Class Class1


    End Class
End Namespace```
They do not yield the same result. In C#, the namespace will override the default namespace and thus you still write this.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.IO;

namespace C_Console
{
    class Program
    {
        static void Main(string[] args)
        {
            C_Class_Library_1.Class1 class1;
        }
    }
}
```
In VB.Net, the namespace gets added to the root namespace and thus you end up with this.

```vbnet
Module Module1

    Sub Main()
        Dim c As New VB_Class_Library_1.VB_Class_Library_1.Class1
    End Sub

End Module
```
You get the same namespace twice.
  
Remarkable.

## Variable declaration

The following examples do exactly the same thing.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace C_Class_Library_1
{
    public class Class1
    {
        int count1;
        private int count2;
        public int count3;
        protected int count4;
        var count5 = 1;

        public void method()
        {
            int counter1;
            var counter2 = 1;
        }
    }
}
```
```vbnet
Public Class Class1
    Dim count1 As Integer
    Private count2 As Integer
    Public count3 As Integer
    Protected count4 As Integer
    Dim count5 = 1

    Public Sub method()
        Dim counter1 As Integer
        Dim counter2 = 1
    End Sub
End Class```
But watch out for inference in VB.Net. In C# 4.0, type inference is turned on by default and you can&#8217;t turn it off. In VB.Net 9.0, you can turn it off and it is turned off by default, if you upgrade your project from an earlier version. 

There are two solutions to solving the inference problem in VB.Net.

First of all, let&#8217;s look at the error message you will get when it is turned off.

Of course, as good VB programmers, we have Option strict on.

```vbnet
Option Infer Off
Option Strict On
Option Explicit On

Public Class Class1
    Dim count1 As Integer
    Private count2 As Integer
    Public count3 As Integer
    Protected count4 As Integer
    Dim count5 = 1

    Public Sub method()
        Dim counter1 As Integer
        Dim counter2 = 1
    End Sub
End Class```
We then get this error message on the lines without an &#8220;As&#8221;.

> Option Strict On requires all variable declarations to have an &#8216;As&#8217; clause. 

What if we turn Option Strict Off? Then we get rid of the error messages. But instead of inferring the type, it will just make the type of the declarations without a type default to Object. If on the other hand we turn on Option Strict and Option Infer, it will infer the type and Dim counter2 = 1 magically becomes of type Integer. However Dim Count = 5 doesn&#8217;t get inferred, so it only infers type inside method declarations when Option Infer and Option strict is on. I would have liked both On but I guess you will have to choose. 

We only get the right result if we do this.

```vbnet
Option Infer On
Option Strict Off
Option Explicit On

Public Class Class1
    Dim count1 As Integer
    Private count2 As Integer
    Public count3 As Integer
    Protected count4 As Integer
    Dim count5 = 1

    Public Sub method()
        Dim counter1 As Integer
        Dim counter2 = 1
    End Sub
End Class```
Which is very weird IMHO.

But we can also do all this at the project level so that we don&#8217;t have to repeat ourselves(DRY). 

You can set them in the compile tab.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/optionsinVB.Net.jpg" alt="" title="" width="950" height="184" />
</div>

This ends part 4, next up, Types.

 [1]: /index.php/DesktopDev/MSTech/vb-net-and-c-the-difference-in-oo-syntax-3
 [2]: http://aspalliance.com/625
 [3]: http://www.jetbrains.com/resharper/