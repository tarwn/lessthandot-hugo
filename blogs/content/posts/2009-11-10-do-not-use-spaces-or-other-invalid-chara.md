---
title: Do not use spaces or other invalid characters in your column names
author: George Mastros (gmmastros)
type: post
date: 2009-11-10T13:16:26+00:00
ID: 619
excerpt: 'Column names (and table names) should not have spaces or any other invalid characters in them.  This is considered bad practice because it requires you to use square brackets around your names.  Square brackets make the code harder to read and understan&hellip;'
url: /index.php/datamgmt/dbprogramming/do-not-use-spaces-or-other-invalid-chara/
views:
  - 286920
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
Column names (and table names) should not have spaces or any other invalid characters in them. This is considered bad practice because it requires you to use square brackets around your names. Square brackets make the code harder to read and understand. The query (presented below) will also highlight columns and tables with numbers in the names. Most of the time, when there is a number in a column name, it represents a de-normalized database. There are exceptions to this rule, so not all occurrences of this problem need to be fixed. 

Based on a comment from Aaron Bertrand, I decided to modify the code below. I recognize that some organizations allow (and may even encourage) the use of the underscore character. In the newly modified code below, you can include a list of acceptable symbols. The code below allows the underscore symbol and the $ symbol. Modify this local variable to include any symbol that is acceptable within your organization.

**How to detect this problem:**

sql
Declare @AcceptableSymbols VarChar(100)

Set @AcceptableSymbols = '_$'

SELECT 'ColumnName' AS Type, 
       Table_Name + '.' + Column_Name AS Problem
FROM   Information_Schema.Columns
WHERE  Column_Name like '%[^a-z' + @AcceptableSymbols + ']%'
 
UNION all
 
SELECT  'TableName', Table_Name
FROM    Information_Schema.Tables
WHERE   Table_Name Like '%[^a-z' + @AcceptableSymbols + ']%'
```

**How to correct it:** If this is a number issue, you may need to redesign your database structure to include more tables. For example, if you have a StudentGrade table with (StudentId, Grade1, Grade2, Grade3, Grade4) you should change it to be StudentGrade with (StudentId, Grade, Identifier). Each student would have multiple rows in this table (one for each grade). You would need to add an identifier column to indicate what the grade is for (test on November 10, book report, etc).

If this is a weird character issue, then you should change the name of the column so it is a simple word or phrase without any spaces, numbers, or symbols. When you do this, make sure you check all occurrences of where this is used from. This could include procedures, function, views, indexes, front end code, etc…

**Level of severity:** mild
  
**Level of difficulty:** moderate