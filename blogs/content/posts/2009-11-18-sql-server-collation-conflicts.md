---
title: SQL Server collation conflicts
author: George Mastros (gmmastros)
type: post
date: 2009-11-18T13:00:09+00:00
ID: 634
excerpt: "Collations control how strings are sorted and compared.  Sorting is not usually a problem because it does not cause collation conflicts.  It may not sort the way you want it to, but it won't cause errors.  The real problem here is when you compare data.&hellip;"
url: /index.php/datamgmt/dbprogramming/sql-server-collation-conflicts/
views:
  - 70427
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
Collations control how strings are sorted and compared. Sorting is not usually a problem because it does not cause collation conflicts. It may not sort the way you want it to, but it won't cause errors. The real problem here is when you compare data. Comparisons can occur several different ways. This can be a simple comparison in a where clause, or a comparison in a join condition. By having columns in your database that do not match the default collation of the database, you have a problem just waiting to happen.

When you add a new column to an existing table or create a new table with string column(s), and you do NOT specify the collation, it will use the default collation of the database. If you then write queries that join with existing columns (that has a different collation) you will get collation conflict errors.

Just to be clear here, I am NOT suggesting that every string column should have a collation that matches the default collation for the database. Instead, I am suggesting that when it is different, there should be a good reason for it. There are many successful databases out there where the developers never give any thought to the collation. In this circumstance, it's best for the collations for each string column match the default collation for the database.

**How to detect this problem:**

```sql
Select  C.Table_Name, Column_Name
From    Information_Schema.Columns C
        Inner Join Information_Schema.Tables T
          On C.Table_Name = T.Table_Name
Where   T.Table_Type = 'Base Table'
        And Collation_Name <> DatabasePropertyEx(db_name(), 'Collation')
        And ColumnProperty(Object_id(C.Table_Name), Column_Name, 'IsComputed') = 0
Order By C.Table_Name, C.Column_Name
```
**How to correct it:** To correct this problem, you can modify the collation for your existing string columns.

**Level of severity:** High
  
**Level of difficulty:** Easy