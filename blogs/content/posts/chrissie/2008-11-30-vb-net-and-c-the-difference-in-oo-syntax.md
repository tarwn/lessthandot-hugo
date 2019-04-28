---
title: 'VB.Net and C# – the difference in OO syntax part 1'
author: Christiaan Baes (chrissie1)
type: post
date: 2008-11-30T14:39:35+00:00
ID: 225
url: /index.php/desktopdev/mstech/vb-net-and-c-the-difference-in-oo-syntax/
views:
  - 3310
categories:
  - Microsoft Technologies
tags:
  - beginner
  - 'c#'
  - vb.net

---
I am a VB.Net developer that writes C# code. I use C# for writing my test projects since Rhino mocks likes C# a lot better then it does VB.Net and we can all blame it on the bad lambda implementation that VB.Net 9.0 has. Lucky for us, this will improve in VB.Net 10.0. 

But I am writing this series of posts about the difference between C# and VB.Net because I am preparing for a promotion exam. The only way to get a promotion when working for the government is via an exam. And those exams are organized by some people in a land far, far away ;-). For this exam I had the choice between three languages Java, C# and ASP.Net. First question that comes to mind is &#8220;Since when is ASP.Net a language?&#8221; and then &#8220;Oh well&#8221;. So I had to choose between C# and Java. Since I don&#8217;t use the Java framework as much as I used to, I chose C#. 

The exam is in two parts. The first part was back in April and I passed but I learned that my C# isn&#8217;t good enough. You see the test is paper and pen only, no IDE help in sight. So knowing the syntax becomes more important and knowing the syntax like the back of my hand is important for me. Enough blah-blah, on to the important stuff.

So to make things easier for me, I will write this series of posts, just for myself. But if it helps someone then that is an added bonus. So here goes part one. The simple things.

If you want to learn the beginnings of C# then here are some sites you could use.

  * [A Twisted Look at Object Oriented Programming in C#][1]
  * [C# BEGINNER TUTORIALS][2]
  * [Free Book: C# Programming for Beginners][3]

## Prerequisites

First we create a solution with 1 C# consoleapplication, 2 C# class libraries, 1 VB.Net consoleapplication and 2 class libraries.

The solution should look like this after you have done.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/Beginners1.jpg" alt="" title="" width="308" height="422" />
</div>

## Classes

First thing to do is see how a standard class is made in both languages.

```vbnet
Public Class Class1

End Class```
```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace C_Class_Library_2
{
    public class Class1
    {
    }
}```
A big difference from the start. To begin with, Public in VB.Net and public in C#: they both do the same so we won&#8217;t really dawdle too much on them.

Then we say that C# uses a namespace by default, in this case this is the project name because that has the same namespace as the projectname (you can change it and have them different if you really like). 

Then we have the using statements. VB.Net doesn&#8217;t seem to have the corresponding Imports statements, this happens to have a simple explanation. Because VB.Net does have them, it just has project/assembly level imports for us, so we don&#8217;t have to type everything in every class.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/Beginners2.jpg" alt="" title="" width="975" height="547" />
</div>

I couldn&#8217;t find this for C# so I guess it isn&#8217;t there. 

I don&#8217;t want to make this post too long, I know you all have a short attention span. [So I will continue in part two][4]. See you tomorrow.

 [1]: http://www.developerfusion.com/article/3821/a-twisted-look-at-object-oriented-programming-in-c/10/
 [2]: http://www.codebeach.com/index.asp?TabID=2&CategoryID=15&SubcategoryID=77
 [3]: http://www.c-sharpcorner.com/UploadFile/mahesh/csp08202007084545AM/csp.aspx
 [4]: /index.php/DesktopDev/MSTech/vb-net-and-c-the-difference-in-oo-syntax-1