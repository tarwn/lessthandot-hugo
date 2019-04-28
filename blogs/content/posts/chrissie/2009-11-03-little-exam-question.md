---
title: Little exam question
author: Christiaan Baes (chrissie1)
type: post
date: 2009-11-03T11:13:19+00:00
ID: 605
url: /index.php/desktopdev/mstech/little-exam-question/
views:
  - 1710
categories:
  - Microsoft Technologies
tags:
  - 'c#'
  - exam

---
I got a nice little exam question today. It was supposed to be all about C# but that&#8217;s another story. 

One of the real C# questions was this.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace ConsoleApplication2
{
    class Program
    {
        static void Main(string[] args)
        {
            try
            {
                Console.WriteLine("A");
                var val = int.Parse("8A");
                Console.WriteLine("B");
            }
            catch(Exception ex)
            {
                Console.WriteLine("C");
                return;
            }
            finally
            {
                Console.WriteLine("D");
                Console.ReadLine();
            }
            Console.WriteLine("E");
        }
    }
}
```
What will be the result? Without running it of course.

Write your answers on a postcard and send it to a close friend (highly unlikely that you have any friends since you read this blog 😉 ) or leave your answer in the comments.