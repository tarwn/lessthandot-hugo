---
title: Don't prefix your table names with tbl
author: George Mastros (gmmastros)
type: post
date: 2009-11-05T11:29:28+00:00
ID: 610
url: /index.php/datamgmt/dbprogramming/don-t-prefix-your-table-names-with-tbl/
views:
  - 31481
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
This is a naming convention issue. Tables should not be prefaced with tbl because it does nothing to add to the clarity of the code (self-documenting). It actually has the opposite effect because it takes longer to read it and causes you to perform an interpretation of the three letter abbreviation.

**How to detect this problem:**

```sql
Select	* 
From	Information_Schema.tables 
Where	Table_Type = 'Base Table'
       	And Table_Name Like 'tbl%'
```
**How to correct it:** Rename the table to remove the prefix. This is not as simple as it seems because this table could be referenced from a number of places, including views, stored procedures, user defined functions, index creation scripts, in-line SQL (embedded within front end applications), etc...

**Level of severity:** mild

**Level of difficulty:** moderate to high