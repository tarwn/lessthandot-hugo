---
title: Don't use text datatype for SQL 2005 and up
author: George Mastros (gmmastros)
type: post
date: 2009-11-17T19:24:01+00:00
ID: 633
url: /index.php/datamgmt/dbprogramming/don-t-use-text-datatype-for-sql-2005-and/
views:
  - 25746
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
With SQL Server versions prior to SQL2005, the only way to store large amounts of data was to use the text, ntext, or image data types. SQL2005 introduced new data types that replace these data type, while also allowing all of the useful string handling functions to work. Changing the data types to the new SQL2005+ equivalent should be relatively simple and quick to implement (depending on the size of your tables). So, why wait? Convert the data types now.

The query presented below will display all the columns in all the tables within your database that are text, ntext or image.

**How to detect this problem:**

```sql
Select  O.Name, 
        col.name as ColName,
        systypes.name
From    syscolumns col 
        Inner Join sysobjects O
          On col.id = O.id
        inner join systypes
          On col.xtype = systypes.xtype
Where   O.Type = 'U'
        And ObjectProperty(o.ID, N'IsMSShipped') = 0
        And systypes.name In ('text','ntext','image')
Order By O.Name, Col.Name
```
**How to correct it:** Change the data type to a SQL2005+ version. Text should be converted to varchar(max), ntext should be converted to nvarchar(max) and image should be converted to varbinary(max).

**Level of severity:** Low

**Level of difficulty:** Easy