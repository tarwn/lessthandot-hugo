---
title: Supported and unsupported datatypes with Hekaton tables
author: SQLDenis
type: post
date: 2013-06-25T08:01:00+00:00
ID: 2113
excerpt: |
  If you are ready to use Hekaton then you need to know that there are some limitations, not all data types are supported. 
  
  The following list contains the set of supported data types in both Hekaton tables and stored procedures:
  
  bit
  All integer ty&hellip;
url: /index.php/datamgmt/dbprogramming/supported-and-unsupported-datatypes-with/
views:
  - 12955
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - hekaton
  - sql server 2014

---
If you are ready to use Hekaton then you need to know that there are some limitations, not all data types are supported. 

The following list contains the set of supported data types in both Hekaton tables and stored procedures:

bit
  
All integer types: tinyint, smallint, int, bigint
  
All money types: money, smallmoney
  
All floating types: float, real
  
Date/time types: datetime2, date, time, datetime, smalldatetime
  
numeric and decimal types
  
All non-LOB string types: char(n), varchar(n), nchar(n), nvarchar(n), sysname
  
Non-LOB binary types: binary(n), varbinary(n)
  
Uniqueidentifier

The data types listed below are unsupported

datetimeoffset
  
varbinary(max)
  
varchar(max)
  
nvarchar(max)
  
xml
  
text
  
image
  
sql_variant

There is also a limit of 8060 bytes on row size, this includes variable length columns. You cannot create a table with two varchar(5000) columns.

if you try to do the following, you will get an error

sql
CREATE TABLE [dbo].[HekaTest]
( [Col1] varchar(5000), 
  [Col2] varchar(4000) NOT NULL,  

  CONSTRAINT [HekaTest_PK] PRIMARY KEY NONCLUSTERED HASH ([Col1]) WITH(BUCKET_COUNT = 1000000)
) WITH (MEMORY_OPTIMIZED = ON, 
 DURABILITY = SCHEMA_AND_DATA)
```

Here is the error that you get

_Msg 41307, Level 16, State 1, Line 4
  
The row size limit of 8060 bytes for memory optimized tables has been exceeded. Please simplify the table definition._

Just be aware of these limitations if you are thinking about using Hekaton. There are also limitations with T-SQL, for example MERGE statement, TRUNCATE statement and identity columns can&#8217;t be used, certain functions can&#8217;t be used either. Make sure to look at the documentation to see what the restrictions are