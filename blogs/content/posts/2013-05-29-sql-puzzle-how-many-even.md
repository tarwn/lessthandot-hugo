---
title: SQL Puzzle…how many even numbers are there in a row
author: SQLDenis
type: post
date: 2013-05-29T07:29:00+00:00
ID: 2087
excerpt: |
  In last week's puzzle SQL Puzzle: RIGHT without using the RIGHT function we looked at how to do a RIGHT function without using the RIGHT function. Today we are going to find out how many columns are even in a row. 
  
  Before starting I want you to be aw&hellip;
url: /index.php/datamgmt/dbprogramming/sql-puzzle-how-many-even/
views:
  - 18792
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
tags:
  - puzzle
  - sql server 2000
  - sql server 2005
  - sql server 2008
  - sql server 2012
  - teaser

---
In last week's puzzle SQL Puzzle: [RIGHT without using the RIGHT function][1] we looked at how to do a RIGHT function without using the RIGHT function. Today we are going to find out how many columns are even in a row. 

Before starting I want you to be aware that 0 is an even number From wikipedia: [Parity of zero][2]

> Zero is an even number. In other words, its parity—the quality of an integer being even or odd—is even. Zero fits the definition of “even number”: it is an integer multiple of 2, namely 0 × 2. As a result, zero shares all the properties that characterize even numbers: 0 is divisible by 2, 0 is surrounded on both sides by odd numbers, 0 is the sum of an integer (0) with itself, and a set of 0 objects can be split into two equal sets.
> 
> Zero also fits into the patterns formed by other even numbers. The parity rules of arithmetic, such as even &#8722; even = even, require 0 to be even. Zero is the additive identity element of the group of even integers, and it is the starting case from which other even natural numbers are recursively defined. Applications of this recursion from graph theory to computational geometry rely on zero being even. Not only is 0 divisible by 2, it is divisible by every integer. In the binary numeral system used by computers, it is especially relevant that 0 is divisible by every power of 2; in this sense, 0 is the “most even” number of all.

Here is the table that you will use for your solution

sql
CREATE TABLE #Puzzle(Col1 int, Col2 int, Col3 int, Col4 int, Col5 int)

INSERT  #Puzzle VALUES (1,2,3,4,5)
INSERT  #Puzzle VALUES (0,1,2,3,4)
INSERT  #Puzzle VALUES (2,2,2,2,2)
INSERT  #Puzzle VALUES (1,1,1,1,1)
INSERT  #Puzzle VALUES (3,2,3,2,3)
INSERT  #Puzzle VALUES (2,3,2,3,2)
```

And here is the expected output

<pre>Col1	Col2	Col3	Col4	Col5	Even
1	2	3	4	5	2
0	1	2	3	4	3
2	2	2	2	2	5
1	1	1	1	1	0
3	2	3	2	3	2
2	3	2	3	2	3</pre>

That should be pretty easy right?

How about a solution that doesn't use a CASE statement, you think you can do it (I can think of 2 ways without using a CASE statement)

Post your solutions as a comment

 [1]: /index.php/DataMgmt/DBProgramming/sql-puzzle-right-without-using
 [2]: http://en.wikipedia.org/wiki/Parity_of_zero