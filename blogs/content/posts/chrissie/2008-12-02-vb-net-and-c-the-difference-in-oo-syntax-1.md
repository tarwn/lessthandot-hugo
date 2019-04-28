---
title: 'VB.Net and C# – the difference in OO syntax part 2'
author: Christiaan Baes (chrissie1)
type: post
date: 2008-12-02T09:01:42+00:00
ID: 226
url: /index.php/desktopdev/mstech/vb-net-and-c-the-difference-in-oo-syntax-1/
views:
  - 5958
categories:
  - Microsoft Technologies
tags:
  - access modifiers
  - beginners
  - 'c#'
  - vb.net

---
[Part 1][1]

  * [Understanding C# Class and Member Modifiers][2]
  * [How to: Control the Availability of a Variable][3]
  * [Access Levels in Visual Basic][4]
  * [Working with .NET access modifiers][5]

## Classes and their access modifiers

So let&#8217;s see what the differences are between the access modifiers of VB.Net and C#. I found this list.

For VB.Net that would be:

  * Private
  * Protected
  * Friend
  * Protected friend
  * Public

In C# that will be:

  * private
  * protected
  * internal
  * protected internal
  * public

So Friend becomes internal in C# and C# uses a different capitalization. That is a good thing to remember.

Now let&#8217;s see if they do the same thing.

### Private or private

Let&#8217;s try something.

In C\_Class\_Library_2.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace C_Class_Library_2
{
    private class Class1
    {
    }
}```
This gives me the error.

> Elements defined in a namespace cannot be explicitly declared as private, protected, or protected internal

So I can&#8217;t do this. In C#, I had to build it before getting this error.

In VB\_Class\_Library_2.

```vbnet
Private Class Class1

End Class```
Slightly different error. 

> Types declared &#8216;Private&#8217; must be inside another type.

The VB.Net error seems to be more to the point. And I didn&#8217;t need to compile.

So lets use Private class for what it is intended.

In C\_Class\_Library_2.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace C_Class_Library_2
{
    public class Class1
    {
        private class Class1_1
        {
        }
    }
}
```
In VB\_Class\_Library_2

```vbnet
Public Class Class1
    Private Class Class1_1

    End Class
End Class
```
These private classes can only be used inside the class where they are declared.

So doing this in the consoleapp will not work.

In C_Console.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace C_Console
{
    class Program
    {
        static void Main(string[] args)
        {
            C_Class_Library_2.Class1_1 class1 = new C_Class_Library_2.Class1_1();
            C_Class_Library_2.Class1.Class1_1 class2 = new C_Class_Library_2.Class1.Class1_1();
        }
    }
}
```
The first line gives this error.

> The type or namespace name &#8216;Class1\_1&#8217; does not exist in the namespace &#8216;C\_Class\_Library\_2&#8217; (are you missing an assembly reference?)

And the second line will give.

> &#8216;C\_Class\_Library\_2.Class1.Class1\_1&#8217; is inaccessible due to its protection level

Now let&#8217;s do the same in VB.Net.

In VB_Console.

```VB.Net
Module Module1

    Sub Main()
        Dim class1 As New VB_Class_Library_2.Class1_1
        Dim class2 As New VB_Class_Library_2.Class1.Class1_1
    End Sub

End Module
```
The first line gives this.

> Type &#8216;VB\_Class\_Library\_2.Class1\_1&#8217; is not defined.

The second line gives.

> &#8216;VB\_Class\_Library\_2.Class1.Class1\_1&#8217; is not accessible in this context because it is &#8216;Private&#8217;.

Now lets see if we can return something of Class1_1.

In C\_Class\_Library_2

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace C_Class_Library_2
{
    public class Class1
    {
        public Class1_1 getClass1_1()
        {
        }

        private class Class1_1
        {
        }
    }
}
```
After compilation we get this error message.

> Inconsistent accessibility: return type &#8216;C\_Class\_Library\_2.Class1.Class1\_1&#8217; is less accessible than method &#8216;C\_Class\_Library\_2.Class1.getClass1\_1()&#8217;

Which is very logical.

In VB\_Class\_Library_2.

```vbnet
Public Class Class1
    Public Function GetClass1_1() As Class1_1

    End Function

    Private Class Class1_1

    End Class
End Class
```
We get this error message.

> &#8216;GetClass1\_1&#8217; cannot expose type &#8216;Class1\_1&#8217; in namespace &#8216;VB\_Class\_Library_2&#8217; through class &#8216;Class1&#8217;.

Seems fine by me.

Apart from a difference in wording, they seem to be both in agreement. I wish the two teams could sit together and get the error messages the same, preferably taking the good from both sides. 

This post is long enough. I think I covered all the basics of private class. You might think this is too basic, but I learned something from it. I could have gone on and on with nesting, but I guess you get the point. [Next up is protected or Protected.][6]

 [1]: /index.php/DesktopDev/MSTech/vb-net-and-c-the-difference-in-oo-syntax
 [2]: http://www.devsource.com/c/a/Techniques/Understanding-C-Class-and-Member-Modifiers/
 [3]: http://msdn.microsoft.com/en-us/library/e8zxe4y5.aspx
 [4]: http://msdn.microsoft.com/en-us/library/76453kax.aspx
 [5]: http://www.builderau.com.au/program/dotnet/soa/Working-with-NET-access-modifiers/0,339028399,339283997,00.htm
 [6]: /index.php/DesktopDev/MSTech/vb-net-and-c-the-difference-in-oo-syntax-3