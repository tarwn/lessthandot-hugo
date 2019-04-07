---
title: 'Best Practice: Every table should have a primary key'
author: George Mastros (gmmastros)
type: post
date: 2009-11-11T12:04:29+00:00
ID: 620
url: /index.php/datamgmt/dbprogramming/best-practice-every-table-should-have-a/
views:
  - 45503
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
By definition, primary keys must contain unique data. They are implemented in the database through the use of a unique index. If there is not already a clustered index on the table, then the primary key&#8217;s index will be clustered. It&#8217;s not always true, but most of the time, you want your primary keys to be clustered because it is usually the key criteria in your requests to the data. This includes join conditions and where clause criteria. Clustered indexes give you exceptional performance because it allows SQL Server to create optimal execution plans.

**How to detect this problem:**

sql
Select AllTables.Name
From   (
       Select Name, id 
       From   sysobjects 
       Where  xtype = 'U'
       ) As AllTables
       Left Join (
         Select parent_obj
         From   sysobjects 
         Where  xtype = 'PK'
         ) As PrimaryKeys
         On AllTables.id = PrimaryKeys.parent_obj
Where  PrimaryKeys.Parent_Obj Is NULL
Order By AllTables.Name
```
**How to correct it:** Identify tables without a primary key using the query above. Examine each table and identify what makes each row in the table unique. Modify the table to include a primary key.

**Level of severity:** Moderate
  
**Level of difficulty:** Easy