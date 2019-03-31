---
title: How to flip a bit in SQL Server by using the Bitwise NOT operator
author: SQLDenis
type: post
date: 2010-09-29T15:30:45+00:00
url: /index.php/datamgmt/dbprogramming/how-to-flip-a-bit-in-sql-server-by-using/
views:
  - 14123
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
tags:
  - bits
  - bitwise
  - bool
  - math
  - sql server 2005
  - sql server 2008
  - tinyint

---
This question came up today.

> in c# i have a:
> 
> a = !a
  
> (if false makes it true, if true makes it false)
> 
> in sql i want to do the same with a BIT variable

In a language like C# you can use ! for a bool to make it become true if it was false and vice versa

So if you run this

<pre>bool a = true;
a = !a;

Console.WriteLine(a.ToString());
a = !a;
Console.WriteLine(a.ToString());
Console.ReadLine();</pre>

The output will be

False
  
True

How can you do this in SQL Server? It is pretty easy, take a look

<pre>select ~ CONVERT(bit,0)
select ~ CONVERT(bit,1)</pre>

The ~ symbol is the Bitwise NOT operator, here is what books on line has to say about the Bitwise NOT operator.

> The ~ bitwise operator performs a bitwise logical NOT for the expression, taking each bit in turn. If expression has a value of 0, the bits in the result set are set to 1; otherwise, the bit in the result is cleared to a value of 0. In other words, ones are changed to zeros and zeros are changed to ones.

Let&#8217;s take a look at another example. What do you think will happen here?

<pre>select ~ CONVERT(tinyint,0)
select ~ CONVERT(tinyint,1)</pre>

That actually returns
  
255
  
254

Why is that? Here is a simple explanation

With a tinyint possible values are between 0 and 255, when you have 0 all bits are turned off, when you flip it, you turn all bits on and you get 255

<pre>00000000	= 0   ( 0 + 0 + 0 + 0 + 0 + 0 + 0 + 0)
11111111	= 255 (1 + 2 + 4 + 8 + 16 + 32 + 64 + 128)</pre>

When you have 1 all bits are turned off except for the first bit, when you flip it you turn all bits on except for the first bit

<pre>00000001	=  1 ( 1 + 0 + 0 + 0 + 0 + 0 + 0 + 0)
11111110	= 254 (0 + 2 + 4 + 8 + 16 + 32 + 64 + 128)</pre>

If you want to use tinyint or int you can use the following code

<pre>select  CONVERT(tinyint,0) ^ 1 as [tinyint], 0^1 as [int]
select  CONVERT(tinyint,1) ^ 1 as [tinyint], 1^1 as [int]</pre>

The ^ operator is the Bitwise Exclusive OR operator. Here is what books on line has to say about Bitwise Exclusive OR

> The ^ bitwise operator performs a bitwise logical exclusive OR between the two expressions, taking each corresponding bit for both expressions. The bits in the result are set to 1 if either (but not both) bits (for the current bit being resolved) in the input expressions have a value of 1. If both bits are 0 or both bits are 1, the bit in the result is cleared to a value of 0. 

Let&#8217;s take a closer look

_The bits in the result are set to 1 if either (but not both) bits (for the current bit being resolved) in the input expressions have a value of 1._

**0 ^ 1**
  
00000000
  
00000001
  
&#8212;&#8212;&#8211;
  
00000001 

_If both bits are 0 or both bits are 1, the bit in the result is cleared to a value of 0._

**1^1**
  
00000001
  
00000001
  
&#8212;&#8212;&#8211;
  
00000000 

So in the end if you want to flip a bit use the Bitwise NOT operator

<pre>select ~ CONVERT(bit,0)
select ~ CONVERT(bit,1)</pre>

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][2] forum**<ins></ins>

 [1]: http://forum.ltd.local/viewforum.php?f=17
 [2]: http://forum.ltd.local/viewforum.php?f=22