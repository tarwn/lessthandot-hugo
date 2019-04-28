---
title: StructureMap and Generics
author: Christiaan Baes (chrissie1)
type: post
date: 2010-07-05T14:43:50+00:00
ID: 835
url: /index.php/desktopdev/mstech/structuremap-and-generics/
views:
  - 5632
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - 'c#'
  - generics
  - structuremap
  - vb.net

---
## Introduction 

I don&#8217;t need to tell you that IoC and an IoC-container is something you should use. My container of preference is [StructureMap][1] by [Jeremy D. Miller][2]. 

One of the more interesting things to map are generic interfaces and their implementations. Generics can save us a lot of typing and make life a lot easier. Less code usually means less bugs. But there are also 2 different ways in which generic interfaces can be implemented &#8211; one is open and the other one is more specific.

## Prerequisites

I used.

  * StructureMap 2.6.1
  * .Net 4.0
  * VB 10
  * C# 4.0
  * VS2010

> <span class="MT_red">StructureMap 2.6.1 won&#8217;t work with the .Net framework 4.0 client profile. It will give you the following warning (I wonder why that is not an error since it will no longer compile)<br /> The referenced assembly &#8220;StructureMap&#8221; could not be resolved because it has a dependency on &#8220;System.Web, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a&#8221; which is not in the currently targeted framework &#8220;.NETFramework,Version=v4.0,Profile=Client&#8221;. Please remove references to assemblies not in the targeted framework or consider retargeting your project.</span>

## The code

Let&#8217;s say we have an interface like this.

```vbnet
Public Interface IGenericInterface(Of T)
    Function ReturnString(ByVal value As T)
End Interface
```
And for the less lucky among us.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace ConsoleApplication2
{
    public interface IGenericInterface&lt;T&gt;
    {
        String ReturnString(T value);
    }
}
```
We can have 2 different ways to implement this interface.

The more generic one ;-).

```vbnet
Public Class GenericClass(Of T)
    Implements IGenericInterface(Of T)

    Public Function ReturnString(ByVal value As T) As Object Implements IGenericInterface(Of T).ReturnString
        Return value.ToString
    End Function
End Class```
And in C#.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace ConsoleApplication2
{
    public class GenericClass&lt;T&gt;: IGenericInterface&lt;T&gt;
    {
        public String ReturnString(T value)
        {
            return value.ToString();
        }
    }
}
```
And a more specific implementation.

```vbnet
Public Class GenericClass2
    Implements IGenericInterface(Of String)

    Public Function ReturnString(ByVal value As String) As Object Implements IGenericInterface(Of String).ReturnString
        Return value & "2"
    End Function

End Class
```
And in C#.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace ConsoleApplication2
{
    public class GenericClass2 : IGenericInterface&lt;String&gt;
    {
        public String ReturnString(String value)
        {
            return value + "2";
        }
    }
}
```
Now lets see if we can get these things mapped.

```vbnet
Imports StructureMap

Module Module1

    Sub Main()
        Dim _container As New Container()
        _container.Configure(Sub(x)
                                 x.For(GetType(IGenericInterface(Of ))).Use(GetType(GenericClass(Of )))
                             End Sub)
        Console.WriteLine(_container.GetInstance(Of IGenericInterface(Of String)).ReturnString("test"))
        Console.WriteLine(_container.GetInstance(Of IGenericInterface(Of Integer)).ReturnString(1))
        Console.ReadLine()
    End Sub

End Module
```
If we run the above then we will get 

> test
  
> 1 

So we are getting GenericClass as a return and not Genericlass2 for the first Console.Writeline.

But we can make it return the more specific implementation like so.

```vbnet
Imports StructureMap

Module Module1

    Sub Main()
        Dim _container As New Container()
        _container.Configure(Sub(x)
                                 x.For(Of IGenericInterface(Of String)).Use(Of GenericClass2)()
                                 x.For(GetType(IGenericInterface(Of ))).Use(GetType(GenericClass(Of )))
                             End Sub)
        Console.WriteLine(_container.GetInstance(Of IGenericInterface(Of String)).ReturnString("test"))
        Console.WriteLine(_container.GetInstance(Of IGenericInterface(Of Integer)).ReturnString(1))
        Console.ReadLine()
    End Sub

End Module
```
Now we get the following result.

> test2
  
> 1 

So it is picking the more specific implementation if it can.

This is the C# version.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using StructureMap;

namespace ConsoleApplication2
{
    class Program
    {
        static void Main(string[] args)
        {
            Container _container = new Container();
            _container.Configure(x =&gt;  {
                                        x.For&lt;IGenericInterface&lt;String&gt;&gt;().Use&lt;GenericClass2&gt;();
                                        x.For(typeof (IGenericInterface&lt;&gt;)).Use(typeof (GenericClass&lt;&gt;));
                                        }
                                );
            Console.WriteLine(_container.GetInstance&lt;IGenericInterface&lt;String&gt;&gt;().ReturnString("test"));
            Console.WriteLine(_container.GetInstance&lt;IGenericInterface&lt;int&gt;&gt;().ReturnString(1));
            Console.ReadLine();
        }
    }
}
```
## Conclusion

Mapping and resolving generic types is very easy with StructureMap. You have no reason whatsoever not to use them and not to use StructureMap to resolve them.

 [1]: http://structuremap.github.com/structuremap/index.html
 [2]: http://codebetter.com/blogs/jeremy.miller/