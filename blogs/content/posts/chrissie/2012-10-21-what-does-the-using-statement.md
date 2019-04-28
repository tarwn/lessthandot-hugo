---
title: What does the Using statement do?
author: Christiaan Baes (chrissie1)
type: post
date: 2012-10-21T06:14:00+00:00
ID: 1763
excerpt: An MSDN article that is wrong about using. And I made the mistake of copying it.
url: /index.php/desktopdev/mstech/what-does-the-using-statement/
views:
  - 3912
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - using
  - vb.net

---
## Introduction

Yesterday there was [a question on the VBIB][1] forum on what the Using statement does and I answered it. But before going on I checked if I was right by looking at [this MSDN article about Using][2]. 

And it states.

```vbnet
' THE FOLLOWING TRY CONSTRUCTION IS EQUIVALENT TO THE USING BLOCK
Dim resource As New resourceType
Try 
    ' Insert code to work with resource.
Catch ex As Exception
    ' Insert code to process exception.
Finally 
    ' Insert code to do additional processing before disposing of resource.
    resource.Dispose() 
End Try ```
That is the 2005 version of that article. The 2010 and 2012 version seem to have a different opinion.

```vbnet
' For the acquisition and disposal of resource, the following
' Try construction is equivalent to the Using block.
Dim resource As New resourceType
Try 
    ' Insert code to work with resource.
Finally 
    If resource IsNot Nothing Then
        resource.Dispose() 
    End If
End Try ```
Another user of VBIB corrected this saying that there is no catch.

So I set out to check and downloaded [Teleriks JustDecompiler][3].

## TestData

First thing I did was to create a project. with this snippet in it.

```vbnet
Imports System.IO

Module Module1

    Sub Main()
        Using s1 = New FileStream("test1", FileMode.Create)

        End Using
    End Sub

End Module
```
## Decompilation

And I then decompiled that.

In IL that would look something like this.

```
IL_0000: nop
    IL_0001: nop
    IL_0002: ldstr "test1"
    IL_0007: ldc.i4.2
    IL_0008: newobj instance void [mscorlib]System.IO.FileStream::.ctor(string,  valuetype [mscorlib]System.IO.FileMode)
    IL_000d: stloc.2
    IL_000e: nop
    .try
    {
        IL_000f: nop
        IL_0010: leave.s IL_0028
    }
    finally
    {
        IL_0012: ldloc.2
        IL_0013: ldnull
        IL_0014: ceq
        IL_0016: ldc.i4.0
        IL_0017: ceq
        IL_0019: stloc.s VB$CG$t_bool$S0
        IL_001b: ldloc.s VB$CG$t_bool$S0
        IL_001d: brfalse.s IL_0026

        IL_001f: ldloc.2
        IL_0020: callvirt instance void [mscorlib]System.IDisposable::Dispose()
        IL_0025: nop

        IL_0026: nop
        IL_0027: endfinally
    }```
and in C# that would recompile to this.

```csharp
FileStream s1 = new FileStream("test1", FileMode.Create);
    try
    {
    }
    finally
    {
        bool flag = s1 != null;
        if (flag)
        {
            s1.Dispose();
        }
    }```
And in VB.Net to this.

```vbnet
Using s1 As FileStream = New FileStream("test1", FileMode.Create)
        If (s1 = Nothing) Then
        End If
    End Using```
I kinda think that the C# version of the decompilation is closer to the truth.

## Conclusion

Did I get the wrong information from that MSDN article or was it different back in 2005? I have no idea. I just know that the 2010 and 2012 versions of that article are closer to the truth. 

And don&#8217;t trust you decompiler to tell you the complete truth either ;-).

 [1]: http://www.vbib.be/index.php?/topic/11648-gebruik-using/#entry63305
 [2]: http://msdn.microsoft.com/en-us/library/htd05whh(v=vs.80).aspx
 [3]: ' THE FOLLOWING TRY CONSTRUCTION IS EQUIVALENT TO THE USING BLOCK Dim resource As New resourceType Try ' Insert code to work with resource. Catch ex As Exception ' Insert code to process exception. Finally ' Insert code to do additional processing before disposing of resource. resource.Dispose() End Try