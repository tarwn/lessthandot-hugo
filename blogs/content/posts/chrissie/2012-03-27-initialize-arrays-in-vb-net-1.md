---
title: Initialize arrays in VB.Net – follow up
author: Christiaan Baes (chrissie1)
type: post
date: 2012-03-27T07:07:00+00:00
ID: 1578
excerpt: |
  So in my previous post I got a comment from Brian.
  
  The declare and initialization syntax used above is generally frowned upon. Rather, you should split the two into separate pieces. Either of the following will give you a 0-sized array while being na&hellip;
url: /index.php/desktopdev/mstech/initialize-arrays-in-vb-net-1/
views:
  - 3891
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - array
  - vb.net

---
So in my [previous post][1] I got a [comment from Brian][2].

> The declare and initialization syntax used above is generally frowned upon. Rather, you should split the two into separate pieces. Either of the following will give you a 0-sized array while being natural:
> 
> &#8216;Full form
  
> Dim array1() As Integer = New Integer() {}
> 
> &#8216;Shortcut
  
> Dim array1() As Integer = {}

I don&#8217;t think that looks any better, but tastes differ. 

It&#8217;s just syntactic sugar anyway.

Here is the code.

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

End Module
```
And here is the resulting IL. Which I decompiled with [justdecompile][3] from [telerik][4].

```
.method public static 
    void Main () cil managed 
{
    .custom instance void [mscorlib]System.STAThreadAttribute::.ctor() = (
        01 00 00 00
    )
    .entrypoint
    .locals init (
        [0] int32[] array1,
        [1] int32[] array2,
        [2] int32[] array3,
        [3] int32[] array4,
        [4] int32[] array5,
        [5] int32[] array6
    )

    IL_0000: nop
    IL_0001: ldc.i4.0
    IL_0002: newarr [mscorlib]System.Int32
    IL_0007: stloc.1
    IL_0008: ldc.i4.1
    IL_0009: newarr [mscorlib]System.Int32
    IL_000e: stloc.2
    IL_000f: ldc.i4.2
    IL_0010: newarr [mscorlib]System.Int32
    IL_0015: stloc.3
    IL_0016: ldc.i4.0
    IL_0017: newarr [mscorlib]System.Int32
    IL_001c: stloc.s array5
    IL_001e: ldc.i4.0
    IL_001f: newarr [mscorlib]System.Int32
    IL_0024: stloc.s array6
    IL_0026: ldc.i4.0
    IL_0027: newarr [mscorlib]System.Int32
    IL_002c: stloc.0
    IL_002d: ldloc.0
    IL_002e: call int32 [System.Core]System.Linq.Enumerable::Count&lt;int32&gt;(class [mscorlib]System.Collections.Generic.IEnumerable`1&lt;int32&gt;)
    IL_0033: call void [mscorlib]System.Console::WriteLine(int32)
    IL_0038: nop
    IL_0039: ldloc.1
    IL_003a: call int32 [System.Core]System.Linq.Enumerable::Count&lt;int32&gt;(class [mscorlib]System.Collections.Generic.IEnumerable`1&lt;int32&gt;)
    IL_003f: call void [mscorlib]System.Console::WriteLine(int32)
    IL_0044: nop
    IL_0045: ldloc.2
    IL_0046: call int32 [System.Core]System.Linq.Enumerable::Count&lt;int32&gt;(class [mscorlib]System.Collections.Generic.IEnumerable`1&lt;int32&gt;)
    IL_004b: call void [mscorlib]System.Console::WriteLine(int32)
    IL_0050: nop
    IL_0051: ldloc.3
    IL_0052: call int32 [System.Core]System.Linq.Enumerable::Count&lt;int32&gt;(class [mscorlib]System.Collections.Generic.IEnumerable`1&lt;int32&gt;)
    IL_0057: call void [mscorlib]System.Console::WriteLine(int32)
    IL_005c: nop
    IL_005d: ldloc.s array5
    IL_005f: call int32 [System.Core]System.Linq.Enumerable::Count&lt;int32&gt;(class [mscorlib]System.Collections.Generic.IEnumerable`1&lt;int32&gt;)
    IL_0064: call void [mscorlib]System.Console::WriteLine(int32)
    IL_0069: nop
    IL_006a: ldloc.s array6
    IL_006c: call int32 [System.Core]System.Linq.Enumerable::Count&lt;int32&gt;(class [mscorlib]System.Collections.Generic.IEnumerable`1&lt;int32&gt;)
    IL_0071: call void [mscorlib]System.Console::WriteLine(int32)
    IL_0076: nop
    IL_0077: call string [mscorlib]System.Console::ReadLine()
    IL_007c: pop
    IL_007d: nop
    IL_007e: ret
}

```
AS you can see array2, array5 and array6 all have ldc.i4.0.

So I conclude that they are all kind of similar.

 [1]: /index.php/DesktopDev/MSTech/initialize-arrays-in-vb-net
 [2]: /index.php/DesktopDev/MSTech/initialize-arrays-in-vb-net#c9825
 [3]: http://www.telerik.com/products/decompiler.aspx
 [4]: http://www.telerik.com