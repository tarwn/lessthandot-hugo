---
title: Procedures with cursors
author: George Mastros (gmmastros)
type: post
date: 2009-11-20T11:27:11+00:00
ID: 638
url: /index.php/datamgmt/dbprogramming/procedures-with-cursors/
views:
  - 11556
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
Looping in SQL code almost always causes performance problems. You can use a cursor or a while loop in SQL code. Both offer similarly bad performance. Not all code can be re-written in a set based manner, but it does make sense to revisit your looping code from time to time to see if it can be re-written.

There are rare times when a cursor will outperform set based code. Not all procedures returned by the query need (or should) be re-written. But it is a worth while endeavor to re-examine these procedures from time to time.

**How to detect this problem:**

sql
Select  Object_Name(Object_ID) As ProcedureName
From    sys.sql_modules S
Where   (Definition Like '%cursor%' or Definition Like '%while%')
        And ObjectProperty(Object_ID, N'IsMSShipped') = 0
Order By Object_Name(Object_ID)
```
**How to correct it:** Examine each block of code that has a loop, and design an alternative method for accomplishing the same results. This is not always possible.

**Level of severity:** moderate

**Level of difficulty:** High