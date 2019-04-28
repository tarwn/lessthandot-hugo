---
title: 'Some more string concatenation weirdness this time with Enums in VB.Net and a difference with C#'
author: Christiaan Baes (chrissie1)
type: post
date: 2011-08-02T16:13:00+00:00
ID: 1286
excerpt: |
  Things don't always do as you expect and testing your code is good for that but sometimes you forget to test the obvious because you just depend on you common sense.
  
  Well Common sense tells me all these methods should give me the same result.
  
  Modu&hellip;
url: /index.php/desktopdev/mstech/some-more-string-concatenation-weirdness/
views:
  - 4632
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET

---
Things don&#8217;t always do as you expect and testing your code is good for that but sometimes you forget to test the obvious because you just depend on you common sense.

Well Common sense tells me all these methods should give me the same result.

```
Module Module1

    Sub Main()
        Console.WriteLine("test " & Test.Test1)
        Console.WriteLine("test " + Test.Test1.ToString)
        Console.WriteLine(String.Format("{0}{1}", "test ", Test.Test1))
        Console.WriteLine(String.Concat("test ", Test.Test1))
        Console.WriteLine(String.Join("", {"test ", Test.Test1}))
        Console.ReadLine()
    End Sub

    Public Enum Test
        Test1
        Test2
        Test3
    End Enum

End Module
```
But I get this as the result.

> test 1
  
> test Test1
  
> test Test1
  
> test Test1
  
> test Test1 

in C# the following. Which is more or less the same.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("test " + Test.Test1);
            Console.WriteLine(String.Format("{0}{1}", "test ", Test.Test1));
            Console.WriteLine(String.Concat("test ", Test.Test1));
            Console.WriteLine(String.Join("", new object[] {"test ", Test.Test1}));
            Console.ReadLine();
        }

        public enum Test
        {
            Test1,
            Test2,
            Test3
        } 
   }
}```
You get this.

> test Test1
  
> test Test1
  
> test Test1
  
> test Test1 

I&#8217;m sure there is a good reason for this inconsistency but I can&#8217;t think of any just yet. At least the first 2 lines should return the same thing in both languages me thinks. I guess if they want to make both languages more compatible than these little things should be worked on. Of course this will totally ruin backward compatibility.