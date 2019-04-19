---
title: A couple of ways to convert an integer to an coordinate with SQL Server
author: SQLDenis
type: post
date: 2013-02-03T14:33:00+00:00
ID: 1962
excerpt: |
  I answered this question from a person who had to convert coordinates stored as an integer to a float
  
  
  I'm receiving data on coordinates as 115949833 and I need it to output as 115.949833 because I need to be able to calculate the mileage between th&hellip;
url: /index.php/datamgmt/dbprogramming/a-couple-of-ways-to/
views:
  - 28519
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
tags:
  - conversion
  - sql advent 2012
  - sql server 2008
  - sql server 2008 r2
  - sql server 2012
  - ssms

---
I answered this question from a person who had to convert coordinates stored as an integer to a float

> I'm receiving data on coordinates as 115949833 and I need it to output as 115.949833 because I need to be able to calculate the mileage between the latitude and longitude coordinates. Both the longitude and latitude values are saved as integers with no decimal places

There are several things you can do to accomplish this:
  
SUBSTRING
  
STUFF
  
LEFT and RIGHT
  
Arithmetic

Let's take a look at how each of them work

**Arithmetic multiplication** 
  
This is pretty simple, just multiply the integer value by 0.000001

```sql
DECLARE @pos int = 115949833 

SELECT CONVERT(decimal(10,6),@pos * 0.000001) --115.949833
```

**Arithmetic division**
  
This is pretty simple as well, instead of multiplying the integer value by 0.000001, we are going to divide the value by 1000000.0

```sql
DECLARE @pos int = 115949833 

SELECT CONVERT(decimal(10,6),@pos /1000000.0) --115.949833
```

**STUFF**
  
The STUFF function is not a widely used function in the SQL Server world. Basically you can use it to inject a character (or series of characters) into a range of characters. If you are using 0 then you are inserting and expanding the string, if you use a value greater than 0 then you will replace some characters

```sql
DECLARE @s varchar(100) = '1111122222'
SELECT STUFF(@S,6,0,'.') 
```

11111.22222

As you can see, when you use 0, the dot gets inserted and everything else gets shifted to the right from position 6

```sql
DECLARE @s varchar(100) = '1111122222'
SELECT STUFF(@S,6,0,'.....')
```

11111.....22222

One or more characters doesn't make a difference, the rest still gets shifted to the right

```sql
DECLARE @s varchar(100) = '1111122222'
SELECT STUFF(@S,6,5,'.....')
```

11111.....

Since we used 5, the 5 left most characters after position 6 get replaced by our dots

```sql
DECLARE @s varchar(100) = '1111122222'
SELECT STUFF(@S,6,2,'.....')
```

11111.....222

Now we used 2 instead of 5, as you can see 3 (5-2) are still there from the original value

Here is then how you would use the STUFF function to inject a dot into the value

```sql
DECLARE @pos int = 115949833 

SELECT CONVERT(decimal(10,6),(STUFF(@pos,4,0,'.'))) --115.949833
```

**LEFT + RIGHT**
  
This is pretty easy, you grab the first 3 characters with left, add a dot and grab the last 6 characters with right

```sql
DECLARE @pos int = 115949833 

SELECT LEFT(@pos,3) +'.' + RIGHT(@pos,6)--115.949833
```

**SUBSTRING**
  
Substring is similar to using left and right but you need to give it a start position and end position. You need first to convert to char or vachar because substring can't be used on an integer data type directly

```sql
DECLARE @pos int = 115949833 

SELECT SUBSTRING(CONVERT(CHAR(9),@pos),1,3) +'.' + SUBSTRING(CONVERT(CHAR(9),@pos),4,6) --115.949833
```

That is all for this post, any other way you would have used?