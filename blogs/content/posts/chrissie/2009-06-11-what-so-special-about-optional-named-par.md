---
title: What so special about Optional/named parameters
author: Christiaan Baes (chrissie1)
type: post
date: 2009-06-11T18:12:59+00:00
ID: 466
url: /index.php/desktopdev/mstech/what-so-special-about-optional-named-par/
views:
  - 3185
categories:
  - Microsoft Technologies
tags:
  - 'c#'
  - named parameters
  - optional parameters
  - vb.net

---
We VB.netters have been having them for years, kinda boring actualy. But I guess you C# guys find them all new and exiting.

Here a small number of the blogs in the blogsphere about them.

  * [C# 4.0 Optional Parameters and C# 4.0 Named Parameters][1]
  * [Named and optional parameters in C# 4.0][2]
  * [C# 4.0 Optional Parameters][3]

This works in VB.Net 2008 (VB9) and VB.Net 2010 (VB10).

<span class="MT_red">Optional parameters have been in VB for a long time not sure when named parameters came in.</span>

```vbnet
Module Module1

    Sub Main()
        Console.WriteLine(test)
        Console.WriteLine(test(b:=3))
        Console.ReadLine()
    End Sub

    Public Function test(Optional ByVal a As Integer = 1, Optional ByVal b As Integer = 2)
        Return a + b
    End Function

End Module```
So now C# can do this too. And now they can actualy use a vb dll to its full extent.

The following only works in C# 4.0 VS 2010.

```csharp
namespace ConsoleApplication1
{
    class Program
    {
 
        static void Main(string[] args)
        {
            Console.WriteLine(test);
            Console.WriteLine(test(b: 3));
            Console.ReadLine();
        }

        public int test(int a = 1, int b = 2)
        {
          return a + b;
        }
    }
}```

 [1]: http://davidhayden.com/blog/dave/archive/2009/06/02/CSharp4OptionalNamedParameters.aspx
 [2]: http://blog.voidnish.com/?p=187
 [3]: http://geekswithblogs.net/michelotti/archive/2009/02/05/c-4.0-optional-parameters.aspx