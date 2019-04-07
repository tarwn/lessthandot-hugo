---
title: Interesting UNION with ORDER BY behavior
author: SQLDenis
type: post
date: 2010-02-05T16:26:59+00:00
ID: 695
url: /index.php/datamgmt/dbprogramming/interesting-union-with-order-by-behaviou/
views:
  - 5682
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
tags:
  - gotcha
  - sql server 2008

---
Here is something interesting to think about

Create this table and insert these 2 rows

sql
create table TableName(id int, name varchar(50))
insert TableName values(1,'bla')
insert TableName values(2,'bla2')
```

Now if you try to do something like this

sql
SELECT TOP 1 ID,Name
   FROM TableName
   ORDER BY Name
   UNION ALL
   SELECT 0,''
```

You will get the following error

**_Server: Msg 156, Level 15, State 1, Line 4
  
Incorrect syntax near the keyword &#8216;UNION&#8217;._**

Interestingly, this same code will work if you use it in the following matter

sql
SELECT TOP 1 *
FROM (
   SELECT TOP 1 ID,Name
   FROM TableName
   ORDER BY Name
   UNION ALL
   SELECT 0,''
) X
ORDER BY ID DESC
```

This same code also works with Common Table Expressions

sql
WITH query AS (
    SELECT TOP 1 ID,Name
   FROM TableName
   ORDER BY Name
   UNION ALL
   SELECT 0,'')


  SELECT TOP 1
         x.id,
         x.name
    FROM query x
ORDER BY x.id DESC
```

My assumption is that the optimizer disregards any order by in the subquery. Still I find this interesting because usually a query will work, you stick it inside another piece of code and it will complain about something

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][2] forum**<ins></ins>

 [1]: http://forum.ltd.local/viewforum.php?f=17
 [2]: http://forum.ltd.local/viewforum.php?f=22