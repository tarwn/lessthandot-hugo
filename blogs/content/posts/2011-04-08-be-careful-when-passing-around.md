---
title: Be careful when passing around parameters, make sure they are the same size and type
author: SQLDenis
type: post
date: 2011-04-08T15:33:00+00:00
excerpt: "Someone tried to figure out why his data was showing the next day when he passed in today's date. If you are not careful to use the same data type and this includes scale and precision as well, you can get some strange results. In this post I will take&hellip;"
url: /index.php/datamgmt/datadesign/be-careful-when-passing-around/
views:
  - 8453
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - best practice
  - data types
  - gotcha
  - pitfalls
  - sql server

---
Someone tried to figure out why his data was showing the next day when he passed in today&#8217;s date. If you are not careful to use the same data type and this includes scale and precision as well, you can get some strange results. In this post I will take a look at date, integer, varchar and decimal data types 



## Dates

When using dates make sure that you are using the same data type, don&#8217;t mix datetime and smalldatetime. If you do, you can get some unexpected results, let&#8217;s take a look

First create this table with a datetime column

<pre>CREATE TABLE TestDatetime(SomeDate DATETIME)
GO</pre>

Now create this proc which accepts a smalldatetime

<pre>CREATE PROC prTestDatetime
@SomeDate SMALLDATETIME
AS 

INSERT TestDatetime VALUES(@SomeDate)

GO</pre>

Now call the procedure with the following value

<pre>DECLARE @d DATETIME
SELECT @d = '2011-04-04 23:59:59.000'


EXEC prTestDatetime @d
GO</pre>

When you check the table now you will see that it has become the next day

<pre>SELECT * FROM TestDatetime</pre>

2011-04-05 00:00:00.000

The query below will illustrate the same problem

<pre>DECLARE @d DATETIME
SELECT @d = '2011-04-04 23:59:59.000'
SELECT CONVERT(DATETIME,@d), CONVERT(SMALLDATETIME,@d)</pre>

Output
  
&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-
  
2011-04-04 23:59:59.000 2011-04-05 00:00:00

What happens is because smalldatetime is accurate to 1 minute, it rounds up to the next hour and thus it becomes the next day
  
Usually stuff like this happens where the table gets changed but someone forgot to also change the procedure, it could take a while until you catch a bug like this because unless you are passing in the last minute of the hour you won&#8217;t see it&#8230;however the fact that the seconds are all 00 should give it away
  


## Integer data type

When dealing with integers, you are in luck because it will just blow up in your face

Create this stored procedure

<pre>CREATE PROC prTestInt
@Someint smallint
AS 

SELECT @Someint
GO</pre>

Run it by passing in something that is greater than the small integer data type can hold

<pre>DECLARE @i int
SELECT @i = 99999


EXEC prTestInt @i
GO</pre>

And here is the error.

_Msg 8114, Level 16, State 5, Procedure prTestInt, Line 0
  
Error converting data type int to smallint._

This is a good thing, you will be able to catch this immediately. At least it doesn&#8217;t do a negative overflow like in some languages



## varchar, nvarchar, char and nchar

varchar, nvarchar, char and nchar have a bunch of interesting inconsistencies, this can really bite you if you are not careful

Here is one example, create the following procedure

<pre>CREATE PROC prTestVarchar
@Somevarchar varchar(3)
AS 

SELECT @Somevarchar
GO</pre>

Now run it like this

<pre>DECLARE @v VARCHAR(10)
SELECT @v = '9999999999'


EXEC prTestVarchar @v
GO</pre>

Output
  
&#8212;&#8212;&#8212;
  
999

Since you specified varchar(3), SQL Server trims everything over 3 bytes

What if you just use varchar?
  
People coming from languages where you define something as a string usually make this mistake. Take a look at this: [Issue inserting text into table from c# proc parameter][1]

Create the following stored procedure

<pre>CREATE PROC prTestVarchar2
@Somevarchar varchar
AS 

SELECT @Somevarchar
GO</pre>

Run the proc

<pre>DECLARE @v VARCHAR(10)
SELECT @v = '9999999999'


EXEC prTestVarchar2 @v
GO</pre>

Output
  
&#8212;&#8212;&#8211;
  
9

In this case SQL Server used a size of 1 since nothing was specified. However when you use varchar in a cast or convert function and you don&#8217;t specify a size, it will default to 30 characters

<pre>SELECT CONVERT(VARCHAR,'1111111111222222222233333333334')</pre>

111111111122222222223333333333

As you can see, the last character is not displayed
  
Take also a look at this post [Always include size when using varchar, nvarchar, char and nchar][2] by George Mastros and this post [Bad habits to kick : declaring VARCHAR without (length)][3] by Aaron Bertrand for some more info



## Decimal/Numeric

Decimal (or numeric) will round down or up if it can&#8217;t hold the whole value
  
Take a look by running this

<pre>DECLARE @d DECIMAL(4,3)
DECLARE @d2 DECIMAL(4,2)
SELECT @d = 1.999

SELECT @d2 = @d

SELECT @d,@d2</pre>

Output
  
&#8212;&#8212;&#8212;&#8212;-
  
1.999 2.00

As you can see 1.999 will round up to 2.00 if your scale is less than the number of digits passed in

If you have to do multiplication you have to be extra careful and have enough space to avoid rounding issues, I deal with this all the time because we have to show 10 digits for scale.

Decimal and numeric will default to (18,0) if you don&#8217;t specify anything when declaring them, see this post [Decimal and Numeric problems when you don&#8217;t specify precision and scale][4] by George Mastros for more info, no need for me to repeat the same.

# Conclusion

Make sure that your data types or data type sizes are the same for variables/parameters and tables, if they are not, you might not notice the problem right away and it can be a real pain in the neck to make the change down the road

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][5] forum or our [Microsoft SQL Server Admin][6] forum**<ins></ins>

 [1]: http://stackoverflow.com/questions/5559582/issue-inserting-text-into-table-from-c-proc-parameter/5559609#5559609
 [2]: /index.php/DataMgmt/DBProgramming/MSSQLServer/always-include-size-when-using-varchar-n
 [3]: http://sqlblog.com/blogs/aaron_bertrand/archive/2009/10/09/bad-habits-to-kick-declaring-varchar-without-length.aspx
 [4]: /index.php/DataMgmt/DataDesign/decimal-and-numeric-problems-when-you-do
 [5]: http://forum.lessthandot.com/viewforum.php?f=17
 [6]: http://forum.lessthandot.com/viewforum.php?f=22