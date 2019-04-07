---
title: SQL Server – Set based random numbers
author: George Mastros (gmmastros)
type: post
date: 2009-01-24T18:35:59+00:00
ID: 298
excerpt: "It's not often that we (as programmers) need random numbers, especially from a database.  When you are testing your queries for performance, it's best to use a large table to do it.  Sometimes you just don't have access to a large table to test with, so&hellip;"
url: /index.php/datamgmt/datadesign/sql-server-set-based-random-numbers/
views:
  - 60180
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server

---
It&#8217;s not often that we (as programmers) need random numbers, especially from a database. When you are testing your queries for performance, it&#8217;s best to use a large table to do it. Sometimes you just don&#8217;t have access to a large table to test with, so you may think about creating many rows in your existing table.

SQL Server has a rand() function that will return a random (fractional) number between 0 and 1.
  
For example:

sql
Select Rand()
--	0.686350654426017
```
The problem with the rand function occurs when you use set based operations.

sql
Select Rand() As RandomNumber
From   (Select 1 As NUM Union All
        Select 2 Union All
        Select 3) As Alias
```
<pre>RandomNumber
----------------------
0.920057583532051
0.920057583532051
0.920057583532051</pre>

Not very random, is it? I mean, the number is random, but it&#8217;s also the same for each row. The purpose of this blog is to demonstrate how to get a random value in set based operations.

There is an interesting technique that you can use to accomplish this. There is a NewId() function in SQL Server that returns a GUID, for example: &#8216;94344EE4-5D7A-45EB-9EBC-7A596B7F90F3&#8217;. NewId does work well for set based operations, for example:

sql
Select Rand() As RandomNumber, NewId() As GUID
From   (Select 1 As NUM Union All
        Select 2 Union All
        Select 3) As Alias
```
<pre>RandomNumber           GUID
---------------------- ------------------------------------
0.130542187318667      D12D6274-CB93-4CDB-B848-9F0FA8B88614
0.130542187318667      C708217F-5204-4284-B76B-A55D87BEFBC0
0.130542187318667      B42D7973-B3E2-420F-8D4D-DC72442D0824
</pre>

Notice that the &#8216;Random Number&#8217; column is the same for each row, but the GUID column is different. We can use this interesting fact to generate random numbers by combining this with another function available in SQL Server. The CHECKSUM function will return an integer hash value based on its argument. In this case, we can pass in a GUID, and checksum will return an integer.

sql
Select Rand() As RandomNumber, 
       NewId() As GUID, 
       Checksum(NewId()) As RandomInteger
From   (Select 1 As NUM Union All
        Select 2 Union All
        Select 3) As Alias
```
<pre>RandomNumber           GUID                                 RandomInteger
---------------------- ------------------------------------ --------------
0.152440960483059      CDEB9F5D-E8A2-4FC7-A5A4-0E0A9CCF9786     84,364,212
0.152440960483059      9ABCEC8F-6C0C-4CEF-BA16-E8F0B50FB399 -1,317,220,961
0.152440960483059      6BD23217-F3FF-4F19-AA7F-C127CA7A4763    976,389,102
</pre>

Before we continue, let&#8217;s take a look at the output, because there are some interesting observations that we need to consider before continuing. RandomInteger can be positive or negative because it&#8217;s limited to the range of an integer, so the values must fall between -2,147,483,648 and 2,147,483,647. 

Most of the time, we want a random number within a certain range of numbers. In most languages, we simply multiply the result of the Rand() function to get this number. Since our RandomInteger is already a whole number, we really can&#8217;t do this. However, we could use the mod operator to guarantee a range of numbers. Mod is the remainder of a division operation, so if we mod a number by 10, we are guaranteed to get a number between -9 and +9. Unfortunately, this is a little misleading because there are 19 possible numbers we can get for this. So, to make sure we get a range to 10 numbers, we need to take the absolute value of the number, and then mod 10. Like this:

sql
Select Rand() As RandomNumber, 
       NewId() As GUID, 
       Abs(Checksum(NewId())) % 10 As RandomInteger
From   (Select 1 As NUM Union All
        Select 2 Union All
        Select 3) As Alias
```
<pre>RandomNumber           GUID                                 RandomInteger
---------------------- ------------------------------------ -------------
0.490769895131745      8237A3F3-98C6-4B31-8F4B-8A80ECC8FBFF 1
0.490769895131745      C231EB65-FA81-4536-B909-44FEC91953D9 9
0.490769895131745      F0AD28C5-8AE4-46A1-B8AC-D8C9F90A5747 4
</pre>

Now, we are guaranteed to get a Random number between 0 and 9. Suppose you want to get a random number between 10 and 15. The range of numbers is 6 (10,11,12,13,14,15). The mod value for this needs to be 6, to get a number in the range of 0 to 5. Then, we add 10 to the result to get a number in the range of 10 to 15.

If you want to generate a random number between -5 and 5, don’t be tempted to remove the absolute value function, because you will get numbers that appears to be random, but are not &#8216;as random&#8217; as they should be. 

In my database, I have a numbers table with 1,000,000 rows. When I run this code:

sql
Select	RandomNumber, Count(*) As NumberCount
From	(
		Select Checksum(NewId()) % 6 As RandomNumber
		From Numbers
		) As A
Group By RandomNumber
Order By RandomNumber
```
<pre>RandomNumber NumberCount
------------ -----------
-5           83539
-4           83443
-3           83372
-2           82931
-1           82950
0            166254
1            83207
2            83635
3            83741
4            83417
5            83511
</pre>

Notice that 0 has double the number of occurrences. Instead, we should write it like this:

sql
Select	RandomNumber, Count(*) As NumberCount
From	(
		Select Abs(Checksum(NewId())) % 11 - 5 As RandomNumber
		From Numbers
		) As A
Group By RandomNumber
Order By RandomNumber
```
<pre>RandomNumber NumberCount
------------ -----------
-5           90889
-4           90794
-3           91365
-2           90476
-1           91104
0            90730
1            90895
2            90815
3            90762
4            91133
5            91037
</pre>

Notice now that the values are &#8216;more&#8217; random than the previous version (where 0 had double the number of occurrences). Since we are talking about random numbers, we wouldn&#8217;t expect there to be the same number of occurrences for each number, but with the previous version, there were double the number of zero&#8217;s, which isn&#8217;t truly random. This version has approximately the same number of zero&#8217;s as any other number, making it &#8216;more random&#8217;.