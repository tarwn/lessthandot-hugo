---
title: 'SQL Advent 2012 Day 2: Data types storage differences'
author: SQLDenis
type: post
date: 2012-12-02T15:53:00+00:00
ID: 1819
excerpt: 'SQL Server has two data types to store character data, both of them come in fixed and variable length sizes. The char and varchar data type uses one byte of store to store one character, the nchar and nvarchar data type uses two bytes of store to store one character. The nchar and nvarchar data types are  used to store unicode of data'
url: /index.php/datamgmt/dbprogramming/data-types/
views:
  - 10139
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - backup
  - conversions
  - log
  - performance
  - sql advent 2012
  - sql server 2008
  - sql server 2008 r2
  - sql server 2012
  - storage

---
This is day two of the [SQL Advent 2012 series][1] of blog posts. Today we are going to take a look at how data types can have an impact in queries and also the size of your database.

## Char vs NChar

SQL Server has two data types to store character data[1], both of them come in fixed and variable length sizes. The char and varchar data type uses one byte of store to store one character, the nchar and nvarchar data type uses two bytes of store to store one character. The nchar and nvarchar data types are used to store unicode of data

Let&#8217;s think about that for a second, what we are saying is that the char and varchar data type can store twice the number of characters in the same amount of store as the nchar and nvarchar data type. Why does this matter, space is cheap right? True, space is getting cheaper but we are also storing more and more data every year.

Now think about what happens you have everything stored as unicode data

  * What happens to your backup and restore process, will it be faster or slower, will the files be bigger if not compressed?
  * What about when transferring the results to and from your database server, are the packets able to store the same number of characters.
  * What about the amount of data on a page, what does this do to indexes and index lookups, how does it affect index maintenance?

**If you don&#8217;t need it, then don&#8217;t use unicode data**.
  
Some examples of what I have seen stored in nchar and nvarchar when realy you shouldn&#8217;t:

Zip Code for US addresses
  
US addresses
  
Social Security Numbers (which were stored in plain text none the less)
  
Integer data (enforced by constraints or the app layer to make sure these were only digits)

Let&#8217;s take a quick look by running some T-SQL

First create these two tables

sql
CREATE TABLE TestChar (SomeCol char(10))
GO

CREATE TABLE TestNChar (SomeCol nchar(10))
GO
```

Now populate each with some data

sql
INSERT TestChar
SELECT TOP 1000000 '1234567890'
FROM sys.sysobjects c1
CROSS JOIN sys.sysobjects c2
CROSS JOIN sys.sysobjects c3
CROSS JOIN sys.sysobjects c4
GO

INSERT TestNChar
SELECT TOP 1000000 '1234567890'
FROM sys.sysobjects c1
CROSS JOIN sys.sysobjects c2
CROSS JOIN sys.sysobjects c3
CROSS JOIN sys.sysobjects c4
GO
```

Let&#8217;s see how much space is used by both tables

sql
EXEC sp_spaceused 'TestChar'

EXEC sp_spaceused 'TestNChar'
```

18,824 KB
  
28,744 KB

If you looked at the reserved column, you will see that the nchar data is using 10 MB more than the char data

## Implicit conversions

Besides the storage increase there is also a problem when querying for data that looks like varchar but is stored as unicode. Run the code below. 

sql
SET SHOWPLAN_TEXT ON
GO
DECLARE @v varchar(10) = '0123456789'

SELECT * FROM TestChar WHERE SomeCol LIKE  @v +'%'
GO

SET SHOWPLAN_TEXT OFF
GO
```
Here is the plan for that query

> |&#8211;Table Scan(OBJECT:([tempdb].[dbo].[TestChar]),
  
> WHERE:([tempdb].[dbo].[TestChar].[SomeCol] like [@v]+&#8217;%&#8217;))

If we look at the plan we can see that this looks pretty good
  
Usually people will sometimes change the datatype of a column but will not change any code that access this column. Let&#8217;s now change the data type of the column to nchar

sql
ALTER TABLE TestChar ALTER COLUMN SomeCol nchar(10)
GO
```

Run the query that gives you the plan again

sql
SET SHOWPLAN_TEXT ON
GO
DECLARE @v varchar(10) = '0123456789'

SELECT * FROM TestChar WHERE SomeCol LIKE  @v +'%'
GO

SET SHOWPLAN_TEXT OFF
GO
```

Here is the plan

> |&#8211;Table Scan(OBJECT:([tempdb].[dbo].[TestChar]),
  
> WHERE:([tempdb].[dbo].[TestChar].[SomeCol] like CONVERT_IMPLICIT(nvarchar(11),[@v]+&#8217;%&#8217;,0)))

As you can see, there is a conversion going on right now.

In order to get rid of the conversion, use the correct data types

sql
SET SHOWPLAN_TEXT ON
GO
DECLARE @v nvarchar(10) = '0123456789'

SELECT * FROM TestChar WHERE SomeCol LIKE  @v +'%'
GO

SET SHOWPLAN_TEXT OFF
GO
```



## Using larger datatypes when it is not needed

I see this problem mostly with the integer data types. Below is a list of the integer data types together with their storage size and range

**tinyint**
  
Storage size is 1 byte. Integer data from 0 through 255. 

**smallint**
  
Storage size is 2 bytes. Integer data from -2^15 (-32,768) through 2^15 &#8211; 1 (32,767). 

**int**
  
Storage size is 4 bytes. Integer data from -2^31 (-2,147,483,648) through 2^31 &#8211; 1 (2,147,483,647). 

**bigint**
  
Storage size is 8 bytes. Integer data from -2^63 (-9,223,372,036,854,775,808) through 2^63-1 (9,223,372,036,854,775,807).

Now imagine facebook with a billion users decided to use bigint as CountryID in their Country table, this key is then uses as a foreign key in the user demographics table. This is wasteful,either use a smallint since we won&#8217;t go through 32 thousand countries in the forseeable feature or use the 2 or 3 character ISO code. The problem is even worse if you have a compound 6 column key and it is used as a foreign key in tons of other tables&#8230;that was real fun to clean up&#8230;.use a surrogate 1 column key in that case&#8230;but be sure to test&#8230;.normalize till it hurts then denormalize till it works&#8230;.I will cover normalization in another post&#8230;just wanted to mention it

That is all for day two of the SQL Advent 2012 series, come back tomorrow for the next one, you can also check out all the posts from last year here: [SQL Advent 2011 Recap][2]

[1] I know there is text and ntext but hose are deprecated

 [1]: /index.php/DataMgmt/DBProgramming/sql-advent-2012-here-is
 [2]: /index.php/DataMgmt/DataDesign/sql-advent-2011-recap