---
title: Solving a slow running query issue with UNION
author: Koen Verbeeck
type: post
date: 2015-02-25T11:38:03+00:00
ID: 3253
url: /index.php/datamgmt/dbprogramming/mssqlserver/solving-a-slow-running-query-issue-with-union/
views:
  - 14757
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server
tags:
  - performance
  - sql
  - sql server
  - syndicated
  - t-sql

---
That's right! I will solve a performance issue by adding a UNION into the query. Interested? Read on!

I recently encountered a curious issue with a query. The query itself wasn't exactly rocket science: it read data from a few tables and calculated the start and the end dates of a contract in the SELECT statement. In an outer query there's a range join with a date dimension to explode the data over the different months. Let me explain that last part. Suppose we have a contract with ID 5 that is valid between January 2015 and March 2015. The range join with the date dimension would thus return 3 rows:

[<img class="alignnone size-full wp-image-3257" src="/wp-content/uploads/2015/02/Exploding.png" alt="Exploding" width="187" height="97" />][1]

The query looked something like this:

```sql
WITH CTE_Contracts AS
(
	SELECT
		 c.ContractID
		,ContractFrom	= IIF( ... some date logic)
		,ContractTo		= IIF( ... some date logic)
	FROM contracts			c
	JOIN contractsdetail	cd ON c.ContractID = cd.ContractID
)
SELECT
	 ContractID
	,ContractMonth = d.[Month]
FROM CTE_Contracts	c
JOIN dateDim		d ON	d.[Date]	BETWEEN c.ContractFrom AND c.ContractTo
						AND	d.[Day]		= 1; -- only get the first of the month 

```
The query is a bit more complex, but you get the idea. On the test server, the query took 1 minute and 24 seconds to return about 90,000 rows. That's a tad slow if you ask me. I didn't see anything wrong with the query (and indexes wouldn't help), so I just blamed it on the server and on the standard edition of SQL Server. That was until I came across a very similar query. That query did about the same thing, but it also fetched data from another table and appended it to the first result set with a UNION. Something like this:

```sql
WITH CTE_Contracts AS
(
	SELECT
		 c.ContractID
		,ContractFrom	= IIF( ... some date logic)
		,ContractTo		= IIF( ... some date logic)
	FROM contracts			c
	JOIN contractsdetail	cd ON c.ContractID = cd.ContractID
	UNION
	SELECT
		 c.ContractID
		,ContractFrom	= IIF( ... some date logic)
		,ContractTo		= IIF( ... some date logic)
	FROM contractsextra		c
	JOIN contractsdetail	cd ON c.ContractID = cd.ContractID
)
SELECT
	 ContractID
	,ContractMonth = d.[Month]
FROM CTE_Contracts	c
JOIN dateDim		d ON	d.[Date]	BETWEEN c.ContractFrom AND c.ContractTo
						AND	d.[Day]		= 1; -- only get the first of the month

```
Now this query returned about 120,000 rows in 6 seconds. What? More rows in less time? How's that possible? Time to take a look at the execution plans. The execution plan of the second query:

[<img class="alignnone size-full wp-image-3258" src="/wp-content/uploads/2015/02/executionplan_1.png" alt="executionplan_1" width="852" height="511" srcset="/wp-content/uploads/2015/02/executionplan_1.png 852w, /wp-content/uploads/2015/02/executionplan_1-300x179.png 300w" sizes="(max-width: 852px) 100vw, 852px" />][2]

You can clearly see the two paths of the union being merged with the hash match after which the results are joined to the date dimension using the nested loops.

The execution plan of the first query is a bit different:

[<img class="alignnone size-full wp-image-3255" src="/wp-content/uploads/2015/02/executionplan_2.png" alt="executionplan_2" width="1013" height="340" srcset="/wp-content/uploads/2015/02/executionplan_2.png 1013w, /wp-content/uploads/2015/02/executionplan_2-300x100.png 300w" sizes="(max-width: 1013px) 100vw, 1013px" />][3]

The nested loops now gives a warning that there is no join predicate. This results in about 5 million rows, which are filtered later on with the Filter operator to the desired 90,000 rows. A bit of unnecessary overhead it seems. So the execution plan of the first query is a bit silly, since it calculates the date columns for the inner select after the join (in the Compute Scalar operator between the Filter and the Nested Loops). In the second query, these columns are calculated before the join and so the Nested Loops can use them as join predicates.

The question is why does SQL Server change behavior? Well, the second query has a UNION operator in the inner query. This means that SQL Server has to compare the two result sets which each other, so the date columns have to be calculated directly in the inner query. Knowing this, we can easily optimize the first query by adding a "dummy UNION":

```sql
WITH CTE_Contracts AS
(
	SELECT
		 c.ContractID
		,ContractFrom	= IIF( ... some date logic)
		,ContractTo		= IIF( ... some date logic)
	FROM contracts			c
	JOIN contractsdetail	cd ON c.ContractID = cd.ContractID
	UNION
	SELECT NULL, NULL, NULL, NULL -- just to enforce performance
)
SELECT
	 ContractID
	,ContractMonth = d.[Month]
FROM CTE_Contracts	c
JOIN dateDim		d ON	d.[Date]	BETWEEN c.ContractFrom AND c.ContractTo
						AND	d.[Day]		= 1; -- only get the first of the month 

```
This extra row with all NULL values will be filtered out by the INNER JOIN with the date dimension. Now the query runs in 3 seconds!

[<img class="alignnone size-full wp-image-3256" src="/wp-content/uploads/2015/02/executionplan_3.png" alt="executionplan_3" width="769" height="168" srcset="/wp-content/uploads/2015/02/executionplan_3.png 769w, /wp-content/uploads/2015/02/executionplan_3-300x65.png 300w" sizes="(max-width: 769px) 100vw, 769px" />][4]

 [1]: /wp-content/uploads/2015/02/Exploding.png
 [2]: /wp-content/uploads/2015/02/executionplan_1.png
 [3]: /wp-content/uploads/2015/02/executionplan_2.png
 [4]: /wp-content/uploads/2015/02/executionplan_3.png