---
title: 'VB.Net and C# – the difference in OO syntax part 3'
author: Christiaan Baes (chrissie1)
type: post
date: 2008-12-03T07:22:12+00:00
ID: 233
url: /index.php/desktopdev/mstech/vb-net-and-c-the-difference-in-oo-syntax-3/
views:
  - 4448
categories:
  - Microsoft Technologies
tags:
  - access modifiers
  - beginners
  - 'c#'
  - vb.net

---
[Part 2][1]

  * [Understanding C# Class and Member Modifiers][2]
  * [How to: Control the Availability of a Variable][3]
  * [Access Levels in Visual Basic][4]
  * [Working with .NET access modifiers][5]

## Classes and their access modifiers

### Protected or protected

Let&#8217;s start with an easy one.

In VB\_Class\_Libraby_2

```vbnet
Protected Class Class1

End Class```
Which gives us a very interesting error message.

> Protected types can only be declared inside of a class.

Which seems more than logical.

Now let&#8217;s see what C# has to say about this.

In C\_Class\_Librabry_2.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace C_Class_Library_2
{
    protected class Class1
    {
    }
}
```
And it seems to come to the same conclusion.

> Elements defined in a namespace cannot be explicitly declared as private, protected, or protected internal

In VB\_Class\_Library_2

```vbnet
Public Class Class1
    Protected Class Class1_1

    End Class
End Class
```
And this.

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
        protected class Class1_1
        {
        }
    }
}
```
Should work just fine.

We can now also state that the protected class will only be available to its parent or to subclasses of the parent.

So if I do this:

In VB\_Class\_Library_2

```vbnet
Public Class Class1
    Protected Class Class1_1

    End Class
End Class

Public Class Class2
    Inherits Class1

    Public Sub New()
        Dim _Class1 As New Class1_1
    End Sub
End Class
```
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
        protected class Class1_1
        {
        }
    }

    public class Class2 : Class1
    {
        public Class2()
        {
            Class1_1 class1_1;
        }
    }
}
```
But I am digressing. I was going to talk about the differences.

### Friend or internal

Here is the biggest difference. They decided to use a different keyword for the same thing, neither of them seem to catch the real essence of what the keyword is trying to say. I can say the same for protected BTW, but I think that would have been hard. A Friend or an Internal class can only be used in the same assembly and not by a referencing assembly. 

In VB\_Class\_Library_2

```vbnet
Friend Class Class1

End Class
```
```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace C_Class_Library_2
{
    internal class Class1
    {
    }
}
```
needless to say, we will get error messages if we try to call them from another project/assembly.

In C_Console

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
            C_Class_Library_2.Class1 class1 = new C_Class_Library_2.Class1();
        }
    }
}
```
You actually get three error messages on this one line of code (Well done). 

2 times this one.

> &#8216;C\_Class\_Library_2.Class1&#8217; is inaccessible due to its protection level

And one of these.

> The type &#8216;C\_Class\_Library_2.Class1&#8217; has no constructors defined

In VB_Console

```vbnet
Module Module1

    Sub Main()
        VB_Class_Library_2.Class1 = New VB_Class_Library_2.Class1()
    End Sub

End Module
```
Here I only get 2 error messages.

2 times 

> &#8216;VB\_Class\_Library_2.Class1&#8217; is not accessible in this context because it is &#8216;Friend&#8217;. 

### Protected Friend or protected internal

This is even more restrictive because it makes it protected and Friend/internal at the same time. I won&#8217;t go deeper into this because we will get more of the same. 

### Public or public

This is the simplest one of all. It makes it so you can use your class everywhere and it is the same for everyone.

### No Access modifiers

This is the most curious one. Do both languages default to the same access modifier when you leave it out?

In C\_Class\_Library_2

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace C_Class_Library_2
{
    class Class1
    {
    }
}
```
In C_Console

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
            C_Class_Library_2.Class1 class1 = new C_Class_Library_2.Class1();
        }
    }
}
```
And now run this and see what error messages you get.

Yes, you get the exact same ones as for Friend/Internal, so we now know that csharp defaults to internal when you specify no access modifier.

Now let&#8217;s see what VB.Net gives us.

In VB\_Class\_Library_2.

```vbnet
Class Class1

End Class
```
and 

In VB_Console

```vbnet
Module Module1

    Sub Main()
        VB_Class_Library_2.Class1 = New VB_Class_Library_2.Class1()
    End Sub

End Module
```
And the error message tells us immediately that it also considers it as friend. 

Next up [Namespaces and variable declaration][6].

 [1]: /index.php/DesktopDev/MSTech/vb-net-and-c-the-difference-in-oo-syntax-1
 [2]: http://www.devsource.com/c/a/Techniques/Understanding-C-Class-and-Member-Modifiers/
 [3]: http://msdn.microsoft.com/en-us/library/e8zxe4y5.aspx
 [4]: http://msdn.microsoft.com/en-us/library/76453kax.aspx
 [5]: http://www.builderau.com.au/program/dotnet/soa/Working-with-NET-access-modifiers/0,339028399,339283997,00.htm
 [6]: /index.php/DesktopDev/MSTech/vb-net-and-c-the-difference-in-oo-syntax-4