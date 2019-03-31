---
title: SQL Server Precision And Scale Problems
author: George Mastros (gmmastros)
type: post
date: 2008-11-24T13:43:49+00:00
url: /index.php/datamgmt/datadesign/sql-server-precision-and-scale-problems/
views:
  - 15979
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Microsoft SQL Server

---
Many people are confused about SQL Server&#8217;s precision and scale. This is unfortunate because choosing the correct values for precision and scale is critically important when you perform math operations using the decimal/numeric data type. The point of this blog is to explain how SQL Server determines the data type for math operations, and the order in which conversions occur.

For example:

<pre>Select 10 / 3           UNION All 
Select 10 / 3.0         UNION All 
Select 10 / 3.00        UNION All 
Select 10 / 3.000       UNION All 
Select 10 / 3.0000      UNION All 
Select 10 / 3.00000     UNION All 
Select 10 / 3.000000    UNION All 
Select 10 / 3.0000000   UNION All 
Select 10 / 3.00000000 </pre>

Let&#8217;s take a close look at the above query so that we can predict the output. Of course, it will help if we know the data types that SQL Server uses. There is a relatively obscure function that you can use to determine the data types. SQL\_VARIANT\_PROPERTY

For the first calculation, we have 10 / 3. We all know the answer is 3 1/3, but how is this expressed in the output?

Well, using the SQL\_VARIANT\_PROPERTY function, we can determine the data type that SQL Server will use.

<pre>Select SQL_VARIANT_PROPERTY(3, 'BaseType') As [Base Type], 
       SQL_VARIANT_PROPERTY(3, 'Precision') As [Precision],
       SQL_VARIANT_PROPERTY(3, 'Scale') As [Scale],

       SQL_VARIANT_PROPERTY(10, 'BaseType') As [Base Type], 
       SQL_VARIANT_PROPERTY(10, 'Precision') As [Precision],
       SQL_VARIANT_PROPERTY(10, 'Scale') As [Scale]</pre>

The output indicates SQL Server will use Integer, Precision 10, scale 0. Using integer math, the output will be 3. Since both values are integer, the result is an integer.

Now, let&#8217;s look at the next one. 10/3.0

<pre>Select SQL_VARIANT_PROPERTY(3.0, 'BaseType') As [Base Type], 
       SQL_VARIANT_PROPERTY(3.0, 'Precision') As [Precision],
       SQL_VARIANT_PROPERTY(3.0, 'Scale') As [Scale],

       SQL_VARIANT_PROPERTY(10, 'BaseType') As [Base Type], 
       SQL_VARIANT_PROPERTY(10, 'Precision') As [Precision],
       SQL_VARIANT_PROPERTY(10, 'Scale') As [Scale]</pre>

This time, we get Numeric(2,1) (for 3.0) and int for the 10. There are well defined (although obscure) rules for math operations. Full rules here: [Precision, Scale, and Length][1]

For division, the rule is:

Precision = p1 &#8211; s1 + s2 + max(6, s1 + p2 + 1)
  
Scale = max(6, s1 + p2 + 1)

p1 represents the precision of the first number. s1 represents the scale of the first number. P2 and S2 represent the second number.

10 / 3.0
  
P1 = 10
  
S1 = 0
  
P2 = 2
  
S2 = 1

Precision = p1 &#8211; s1 + s2 + max(6, s1 + p2 + 1)
  
Precision = 10 &#8211; 0 + 1 + max(6, 0 + 2 + 1)
  
Precision = 11 + Max(6, 3)
  
Precision = 11 + 6
  
Precision = 17

Unfortunately, this isn&#8217;t correct because the SQL\_VARIANT\_PROPERTY is returning 10 for the integer. When dividing the numbers, SQL Server actually converts the integer to a decimal, using the smallest value possible to represent the value. In this case, 10 is converted to decimal(2,0). Performing the calculations again:

10 / 3.0
  
P1 = 2
  
S1 = 0
  
P2 = 2
  
S2 = 1

Precision = p1 &#8211; s1 + s2 + max(6, s1 + p2 + 1)
  
Precision = 2 &#8211; 0 + 1 + max(6, 0 + 2 + 1)
  
Precision = 3 + Max(6, 3)
  
Precision = 3 + 6
  
Precision = 9

Scale = max(6, s1 + p2 + 1)
  
Scale = max(6, 0 + 2 + 1)
  
Scale = max(6,3)
  
Scale = 6

So, Select 10 / 3.0 = 3.333333

Now, let&#8217;s fast forward to the last calculation. Select 10 / 3.00000000

Precision/scale for the 10 (after converting to decimal) = 2,0
  
Precision/scale for the 3.00000000 = 9,8

10 / 3.00000000
  
P1 = 2
  
S1 = 0
  
P2 = 9
  
S2 = 8

Precision = p1 &#8211; s1 + s2 + max(6, s1 + p2 + 1)
  
Precision = 2 &#8211; 0 + 8 + max(6, 0 + 9 + 1)
  
Precision = 10 + Max(6, 10)
  
Precision = 10 + 10
  
Precision = 20

Scale = max(6, s1 + p2 + 1)
  
Scale = max(6, 0 + 9 + 1)
  
Scale = max(6,10)
  
Scale = 10

The result is 3.3333333333 Decimal(20,10)

Lastly, since the original example was a UNION ALL query, all results are converted to the same data type. Each result is first calculated, then finally converted to a data type that satisfies all results. In this case, each result is converted to a Decimal(20,10). But remember, this ONLY occurs after each calculation is performed!

<pre>Select 10 / 3           3.0000000000 Int
Select 10 / 3.0         3.3333330000 Decimal(9,6)
Select 10 / 3.00        3.3333330000 Decimal(10,6)
Select 10 / 3.000       3.3333330000 Decimal(11,6)
Select 10 / 3.0000      3.3333330000 Decimal(12,6)
Select 10 / 3.00000     3.3333333000 Decimal(14,7)
Select 10 / 3.000000    3.3333333300 Decimal(16,8)
Select 10 / 3.0000000   3.3333333330 Decimal(18,9)
Select 10 / 3.00000000  3.3333333333 Decimal(20,10)</pre>

 [1]: http://msdn.microsoft.com/en-us/library/ms190476.aspx