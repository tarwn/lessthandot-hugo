---
title: Calculating The Percentage Of Null Values In A Column With Transact SQL
author: SQLDenis
type: post
date: 2009-10-02T12:31:24+00:00
ID: 574
url: /index.php/datamgmt/dbprogramming/calculating-the-percentage-of-null-value/
views:
  - 24225
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
tags:
  - how to
  - 'null'
  - sql
  - tip

---
What is the percentage of null values in a table for a column? This question comes up every now and then and it is pretty easy to answer this question

We will start by creating the following table

```sql
CREATE TABLE #perc ( Column1 INT,Column2 INT,Column3 INT)
INSERT INTO #perc
SELECT NULL,1,1
UNION ALL
SELECT 1,1,1
UNION ALL
SELECT NULL,NULL,1
UNION ALL
SELECT NULL,1,NULL
UNION ALL
SELECT NULL,1,1
UNION ALL
SELECT 1,1,NULL
UNION ALL
SELECT NULL,1,1
UNION ALL
SELECT 2,1,2
UNION ALL
SELECT 3,1,1
```

There are a couple of ways to calculate this but first we need to understand one thing: COUNT(*) and COUNT(ColumnName) behave differently, **COUNT(*) will count NULLS while COUNT(ColumnName) does not!!!**

Here is one way to calculate the percentages, you use the following formula

```sql
SUM(CASE WHEN ColumnName IS NULL THEN 1 ELSE 0 END) / COUNT(*)
```

Run this query below

```sql
SELECT 100.0 * SUM(CASE WHEN Column1 IS NULL THEN 1 ELSE 0 END) / COUNT(*) AS Column1Percent,
100.0 * SUM(CASE WHEN Column2 IS NULL THEN 1 ELSE 0 END) / COUNT(*) AS Column2Percent,
100.0 * SUM(CASE WHEN Column3 IS NULL THEN 1 ELSE 0 END) / COUNT(*) AS Column3Percent
FROM #perc
```

output

<pre>Column1Percent	Column2Percent	Column3Percent
55.555555555555	11.111111111111	22.222222222222</pre>

Instead of using SUM and a CASE statement you can also just do the formula below to get the percentage of NULLS

```sql
(COUNT(*) - COUNT(ColumnName)) / COUNT(*)
```

The query below will return the same output as the query above

```sql
SELECT 100.0 * (COUNT(*) - COUNT(Column1)) / COUNT(*) AS Column1Percent,
100.0 * (COUNT(*) - COUNT(Column2)) / COUNT(*) AS Column2Percent,
100.0 * (COUNT(*) - COUNT(Column3)) / COUNT(*) AS Column3Percent
FROM #perc
```

output

<pre>Column1Percent	Column2Percent	Column3Percent
55.555555555555	11.111111111111	22.222222222222</pre>

What if you want to get a percentage of all values in the column? So for example we will take Column3 from the table

```sql
select Column3 from #perc
```

<pre>1
1
1
NULL
1
NULL
1
2
1</pre>

As you can see we have
  
2 rows with a value of NULL = 22.22% (select (2.0/9.0) * 100)
  
1 row with a value of 2 = 11.11% (select (1.0/9.0) * 100
  
6 rows with a value of 1 = 22.22% (select (6.0/9.0) * 100)

Here is the query which accomplishes this requirement

```sql
SELECT COALESCE(CONVERT(VARCHAR(50),Column3),'NULL') AS Value,
COUNT(Column3) AS ValueCount,
100.0 * COUNT(*)/(SELECT COUNT(*) FROM #perc ) AS Percentage
FROM #perc
GROUP BY Column3
ORDER BY Percentage DESC
```

And here is the output
  
Output

<pre>Value	ValueCount	Percentage
1	6		66.666666666666
NULL	0		22.222222222222
2	1		11.111111111111</pre>

That is all, as you can see it is pretty trivial to calculate NULLS in a column



\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][2] forum**<ins></ins>

 [1]: http://forum.ltd.local/viewforum.php?f=17
 [2]: http://forum.ltd.local/viewforum.php?f=22