---
title: Always include precision and scale with decimal and numeric
author: George Mastros (gmmastros)
type: post
date: 2009-11-09T11:59:00+00:00
ID: 615
excerpt: 'When you use the decimal (or numeric) data type, you should always identity the precision and scale for it.  If you do not, the precision defaults to 18, and the scale defaults to 0.  When scale is 0, you cannot store fractional numbers.  If you do not&hellip;'
url: /index.php/datamgmt/dbprogramming/always-include-precision-and-scale-with/
views:
  - 331942
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
When you use the decimal (or numeric) data type, you should always identity the precision and scale for it. If you do not, the precision defaults to 18, and the scale defaults to 0. When scale is 0, you cannot store fractional numbers. If you do not want to store fractional numbers, then you should use a different data type, like bigint, int, smallint, or tinyint. 

**How to detect this problem:**

SQL 2005 +

sql
-- SQL 2005 +
Select	Name 
From	sys.sysobjects  
Where	XType = 'P'
        And (Object_Definition(ID) Like '%decimal[^(]%'
        Or Object_Definition(ID) Like '%numeric[^(]%')
        And ObjectProperty(ID, N'IsMSShipped') = 0
Order By Name
```
SQL 2000

sql
SELECT  Name
FROM    (
        SELECT S.Name, C.TEXT
        FROM   sysobjects S
               INNER Join syscomments C
                 ON  S.id = C.id
                 And S.xtype = 'P'
        WHERE   OBJECTPROPERTY(S.ID, N'IsMSShipped') = 0
 
        UNION All
 
        SELECT OBJECT_NAME(A.id), LeftText + RightText
        FROM   sysobjects s
               INNER Join (
                 SELECT Id, RIGHT(TEXT, 10) AS LeftText, ColId
                 FROM   syscomments
                 ) AS A
                   ON  S.id = A.id
                   And OBJECTPROPERTY(S.ID, N'IsMSShipped') = 0
                   And S.xtype = 'P'
               INNER Join (
                 SELECT Id, LEFT(TEXT, 10) AS RightText, ColId
                 FROM   syscomments
                 ) AS B
                   ON  A.id = B.id
                   and A.ColId = B.ColId - 1
        ) AS A
WHERE   TEXT Like '%decimal[^(]%'
		Or TEXT Like '%[^i][^s]numeric[^(]%'
ORDER BY Name
```
**How to correct it:** Use the query above to locate this problem with your code. Specify the precision and scale. This will often times require that you look up the proper precision and scale in a table definition.

**Level of severity:** High

**Level of difficulty:** Easy