---
title: Collation conflicts with temp tables and table variables.
author: George Mastros (gmmastros)
type: post
date: 2009-11-19T13:55:10+00:00
url: /index.php/datamgmt/dbprogramming/collation-conflicts-with-temp-tables-and/
views:
  - 28314
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
When the collation of your user database does not match the collation of TempDB, you have a potential problem. Temp tables and table variables are created in TempDB. When you do not specify the collation for string columns in your table variables and temp tables, they will inherit the default collation for TempDB. Whenever you compare and/or join to the temp table or table variable, you may get a collation conflict.

Under normal circumstances, it is best if all your collations match. This includes TempDB, Model (used for creating a new database), your user database, and all your string columns (varchar, nvarchar, char, nchar, text, ntext).

**How to detect this problem:**

<pre>Select	'Warning: Collation conflict between user database and TempDB' As Warning
Where	DatabasePropertyEx('TempDB', 'Collation') <> DatabasePropertyEx(db_name(), 'Collation')</pre>

**How to correct it:** There are several ways to correct this problem. The long term solution is to change the default collation for your database (affecting new string columns) and then change the collation for your existing columns. Alternatively, you could modify any code that creates a temp table or table variable so that it specifies a collation on your string columns. You can hard code the collation or use the default database collation.

ex:

<pre>Create Table #AnyNameYouWant(Id Int, EyeColor VarChar(20) Collate Database_Default)</pre>

**Level of severity:** High. This is a hidden, hard to find bug, just waiting to happen.

**Level of difficulty:** Moderate.