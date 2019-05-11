---
title: 'Handling of events a small difference between C# and VB.Net'
author: Christiaan Baes (chrissie1)
type: post
date: 2011-05-31T12:09:00+00:00
ID: 1196
excerpt: |
  Introduction
  
  This one was hard to figure out why it happened in VB.Net but it you can do it. I'm still not really sure and might need some better understanding why it is permitted. And why it is not permitted in C#, which seemed the more obvious choi&hellip;
url: /index.php/desktopdev/mstech/handling-of-events-a-small/
views:
  - 2136
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - 'c#'
  - events
  - vb.net

---
## Introduction

This one was hard to figure out why it happened in VB.Net but it you can do it. I&#8217;m still not really sure and might need some better understanding why it is permitted. And why it is not permitted in C#, which seemed the more obvious choice. What am I talking about I hear you ask. Look at the code and find out.

## The VB code

```vbnet
Module Module1

	Public WithEvents t As New TestEvents

	Sub Main()
		t.DoTest1()
		t.DoTest2()
		Console.ReadLine()
	End Sub

	Public Class TestEvents
		Public Event test1()
		Public Event test2(ByVal s As String)

		Public Sub DoTest1()
			RaiseEvent test1
		End Sub

		Public Sub DoTest2()
			RaiseEvent test2("")
		End Sub
	End Class

	Private Sub t_test1() Handles t.test1, t.test2
		Console.WriteLine("test")
	End Sub
End Module
```
The result is this.

> test
  
> test 

Do you see the &#8220;problem&#8221;?

Event test1 and event test2 do not share the same signature but they are handled by the same method. Apparently this is permitted as long as the handling method has an empty arguments list.

As soon as you add a parameter then it will no longer compile.

Lucian Wischik put&#8217;s it like this.

> VB allows "zero-argument relaxation", which is typesafe.

## The C# code

This is similar C# code.

```csharp
using System;

namespace ConsoleApplication2
{
    class Program
    {
        private static TestEvents t = new TestEvents();

        static void Main(string[] args)
        {
            t.test1 += t_test1;
            t.test2 += t_test1;
            t.DoTest1();
            t.DoTest2();
            Console.ReadLine();
        }

        public class TestEvents
        {
            public delegate void deltest1();
            public delegate void deltest2(String s);

            public event deltest1 test1;
            public event deltest2 test2;

            public void DoTest1()
            {
                test1();
            }

            public void DoTest2()
            {
                test2("");
            }
        }

        public static void t_test1()
        {
            Console.WriteLine("test");
        }

    }
}
```
The above will not compile. And you will get an error on this line <code class="codespan">t.test2 += t_test1;</code>

## Conclusion

Yet again, do you need to know this? Probably not, I would recommend not using that technique in your code anywhere since it is kinda confusing.