---
title: How to pad positive and negative number with zeroes in SQL Server?
author: SQLDenis
type: post
date: 2009-04-24T13:29:34+00:00
url: /index.php/datamgmt/datadesign/how-to-pad-positive-and-negative-number/
views:
  - 24318
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - formatting
  - sql
  - sql server
  - t-sql

---
You have a table with integer values and you are required to always show 8 numbers, if the length of the number is less than 8 characters then you need to pad it. Of course stuff like this should be done at the presentation layer but we all know that sometimes that means reinstalling apps so SQL is the easiest way. Numbers like these are usually order or customer numbers.
  
The easiest way to pad a number in SQL is by using the following syntax

<pre>SELECT RIGHT('00000000' + 12345,8)</pre>

However running that will still not pad the number with zeroes. You need to convert the number to a varchar first. Run the query below

<pre>SELECT RIGHT('00000000' + CONVERT(VARCHAR(8),12345),8)</pre>

That returns the output that we want

00012345

Let&#8217;s continue by creating a table and dumping some numbers in that table

<pre>CREATE TABLE #Numbers(Num INT)
    INSERT #Numbers VALUES('1')
    INSERT #Numbers VALUES('12')
    INSERT #Numbers VALUES('123')
    INSERT #Numbers VALUES('1234')
    INSERT #Numbers VALUES('12345')
    INSERT #Numbers VALUES('123456')
    INSERT #Numbers VALUES('1234567')
    INSERT #Numbers VALUES('12345678')
    INSERT #Numbers VALUES('123456789')</pre>

Now run the following query

<pre>SELECT RIGHT('00000000' + CONVERT(VARCHAR(8),Num),8)
    FROM #Numbers</pre>

(output)
  
00000001
  
00000012
  
00000123
  
00001234
  
00012345
  
00123456
  
01234567
  
12345678
  
0000000*

As you can see the last row has the value 0000000*. This is because converting to varchar(8) truncated the value. If we increase our convert and right functions to use 9 instead of 8 characters we are fine. Run the same query again.

<pre>SELECT RIGHT('00000000' + CONVERT(VARCHAR(9),Num),9)
    FROM #Numbers</pre>

(output)
  
000000001
  
000000012
  
000000123
  
000001234
  
000012345
  
000123456
  
001234567
  
012345678
  
123456789

As you can see it all looks fine now

What about negative values? What if you want to show -00000123 instead of -123?
  
First insert these 4 rows

<pre>INSERT #Numbers VALUES('-122')
 INSERT #Numbers VALUES('-1')
 INSERT #Numbers VALUES('-777777')
 INSERT #Numbers VALUES('-123456789')</pre>

Now we will run the same query again

<pre>SELECT RIGHT('00000000' + CONVERT(VARCHAR(9),Num),9)
    FROM #Numbers</pre>

Here is what those 4 rows look like that we just inserted

(output)
  
00000-122
  
0000000-1
  
00-777777
  
00000000*

That is not good. Here is what we will do, if the number is negative we will start with a minus sign otherwise we will use a blank and then we will concatenate and replace the minus sign with a blank. This is what it looks like in SQL

<pre>SELECT CASE  WHEN Num < 0
THEN '-' ELSE '' END + RIGHT('000000000' + REPLACE(Num,'-',''), 9)
 FROM #Numbers</pre>

And here is the output

(output)
  
000000001
  
000000012
  
000000123
  
000001234
  
000012345
  
000123456
  
001234567
  
012345678
  
123456789
  
-000000122
  
-000000001
  
-000777777
  
-123456789

As you can see it is not that difficult to do stuff like this 🙂

I also updated the post on our wiki page: [Adding Leading Zeros To Integer Values][1] which is part of our [SQL Server Programming Hacks][2] wiki section

\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][3] forum or our [Microsoft SQL Server Admin][4] forum**<ins></ins>

 [1]: http://wiki.ltd.local/index.php/Adding_Leading_Zeros_To_Integer_Values
 [2]: http://wiki.ltd.local/index.php/SQL_Server_Programming_Hacks_-_100%2B_List
 [3]: http://forum.lessthandot.com/viewforum.php?f=17
 [4]: http://forum.lessthandot.com/viewforum.php?f=22