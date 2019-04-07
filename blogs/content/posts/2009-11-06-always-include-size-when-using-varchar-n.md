---
title: Always include size when using varchar, nvarchar, char and nchar
author: George Mastros (gmmastros)
type: post
date: 2009-11-06T13:33:50+00:00
ID: 613
url: /index.php/datamgmt/dbprogramming/always-include-size-when-using-varchar-n/
views:
  - 132341
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
There are several string data types in SQL Server. There are varchar, nvarchar, char, and nchar. Most front end languages do not require you to identify the length of string variables, and SQL Server is no exception. When you do not specify the length of your string objects in SQL Server, it applies its own defaults. For most things, the default length is one character. This applies to columns, parameters, and locally declared variables. The notable exception is with the cast and convert functions which default to 30 characters.

**How to detect this problem:**

SQL 2005 (and up)

sql
Select  Name 
From    sys.sysobjects 
Where   XType = 'P'
        And Object_Definition(ID) Like '%varchar[^(]%'
        And ObjectProperty(ID, N'IsMSShipped') = 0
Order By Name
```
SQL2000

sql
Select  Name
From    (
        Select S.Name, C.Text
        From   sysobjects S
               Inner Join syscomments C
                 On  S.id = C.id
                 And S.xtype = 'P'
        Where   ObjectProperty(S.ID, N'IsMSShipped') = 0

        Union All

        Select object_Name(A.id), LeftText + RightText
        From   sysobjects s
               inner Join (
                 Select Id, Right(Text, 10) As LeftText, ColId
                 From   syscomments
		 ) As A
                   On  S.id = A.id
                   And ObjectProperty(S.ID, N'IsMSShipped') = 0
                   And S.xtype = 'P'
               Inner Join (
                 Select Id, Left(Text, 10) As RightText, ColId
		 From   syscomments
	         ) As B
                   On  A.id = B.id
                   and A.ColId = B.ColId - 1
        ) As A
Where   Text Like '%varchar[^(]%'
Order By Name
```
**How to correct it:** To correct this problem, identify where it exists (using the SQL shown above). Then, for each occurrence, identify the size. If the problem occurred when declaring a column in a table and you WANT the size to be 1 character, then specify (1). Other places, you will need to determine the size that it should be. Sometimes this involves looking up the size in the table definition.

**Level of severity:** High, because this problem can corrupt your data.

**Level of difficulty:** Easy.