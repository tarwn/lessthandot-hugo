---
title: 'How a VB.Net programmer can annoy his C# colleague: The curious case of case insensitivity'
author: Christiaan Baes (chrissie1)
type: post
date: 2011-08-14T05:51:00+00:00
ID: 1298
excerpt: |
  One of the big differences between C# and VB.Net is the fact that VB.Net is case insensitive and C# isn't.
  The case-insensitivity of VB.Net is sometimes weird and can lead to unwelcome side effects. Like you will see.
url: /index.php/desktopdev/mstech/how-a-vb-net-programmer/
views:
  - 6306
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - 'c#'
  - vb.net

---
## Introduction

One of the big differences between C# and VB.Net is the fact that VB.Net is case insensitive and C# isn&#8217;t.

The case-insensitivity of VB.Net is sometimes weird and can lead to unwelcome side effects. Like you will see.

## Partial classes

For example. the following is completely legal in VB.Net

```vbnet
Module Module1

    Sub Main()
        Dim t = New Test
        t.Test()
        t.partialtest()
        Dim t2 = New test
        t2.Test()
        t2.partialtest()
        Console.ReadLine()
    End Sub
    
End Module

Public Class test
    Public Sub Test()
        Console.WriteLine("test")
    End Sub
End Class

Partial Public Class Test
    Public Sub partialtest()
        Console.WriteLine("partialtest")
    End Sub
End Class
```
Watch the casing of the classes.

See the instantiations?

In C# we would use it like this.

```csharp
using System;

namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
            var t = new ConsoleApplication2.test();
            t.Test();
            t.partialtest();
            Console.ReadLine();
        }

   }
}
```
In other words the name and casing of the class not the partial one. It won&#8217;t work with the partial one. 

So next time they try to convert your VB.Net code to C# they will have a problem with all the different casings and none of it will really work and if your code is big enough it will even hardly make sense.

## WHY?????

The question begs of course, why would you do this?? <span class="MT_red">You don&#8217;t!!</span> But when you rename your class via Visual studio or just by deleting and retyping the characters than the partial class will not be renamed and in most cases you will not even notice because the partial class is in another file. 

## Conclusion

Be very careful with this kind of unwanted behavior you could upset someone.