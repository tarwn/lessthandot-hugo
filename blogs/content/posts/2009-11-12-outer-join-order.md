---
title: OUTER JOIN order
author: chopstik
type: post
date: 2009-11-12T23:50:35+00:00
ID: 598
url: /index.php/datamgmt/dbprogramming/mssqlserver/outer-join-order/
views:
  - 17003
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server

---
I'm almost embarrassed to publish this but I have operated for years under a false assumption that I only recently discovered to be untrue. Or, to perhaps state it better, to be unnecessary. When using an outer join, I have always tended to put the ON operators in the order of the join itself. So, for example, if my first table in a left join was table1, then my ON operators would always be table1.field1 = table2.field1. A more clear example is below.

sql
SELECT a.[field1], a.[field2]
FROM table1 a LEFT JOIN table2 b ON a.[field3] = b.[field3] AND a.[field1] = b.[field1]
WHERE b.[field1] IS NULL
```

However, that is syntactically the same as the following (note where the a.[fieldname] and b.[fieldname] now switch around).

sql
SELECT a.[field1], a.[field2]
FROM table1 a LEFT JOIN table2 b ON b.[field3] = a.[field3] AND a.[field1] = b.[field1]
WHERE b.[field1] IS NULL
```

Both of the code samples above do the same thing. The only issue when using an outer join is the order in which the tables are presented, not the operators in the ON part of the join.

However, that being said, I will still write my code in that fashion as I think it is easier to read and maintain. If nothing else, it is adherence to a standard. So, even if that doesn't match your own standard, it is important to establish a standard and maintain it which will make it easier for future maintenance.