---
title: 'T-SQL Window Functions – Part 2: Ranking Functions'
author: Steve Hughes (DataOnWheels)
type: post
date: 2012-09-11T17:33:00+00:00
ID: 1715
excerpt: 'This is part 2 in my series on SQL window functions.  In this post, we will explore using ranking functions.  SQL Server support four different ranking functions which are supported in SQL Server versions 2005 and forward.  All of these functions requir&hellip;'
url: /index.php/datamgmt/dbprogramming/mssqlserver/t-sql-window-functions-part-2/
views:
  - 13738
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server

---

  
This is part 2 in my series on SQL window functions. In this post, we will explore using ranking functions. SQL Server support four different ranking functions which are supported in SQL Server versions 2005 and forward. All of these functions require the use of the OVER clause.
  
The following functions are classified as ranking functions:
  
• ROW_NUMBER
  
• RANK
  
• DENSE_RANK
  
• NTILE
  
Once again, the following CTE will be used as the query in all examples throughout the post:</p> 

<code class="codespan">with CTEOrders as<br />
	(select cast(1 as int) as OrderID, cast('3/1/2012' as date) as OrderDate, cast(10.00 as money) as OrderAmt, 'Joe' as CustomerName<br />
	union select 2, '3/1/2012', 11.00, 'Sam'<br />
	union select 3, '3/2/2012', 10.00, 'Beth'<br />
	union select 4, '3/2/2012', 15.00, 'Joe'<br />
	union select 5, '3/2/2012', 17.00, 'Sam'<br />
	union select 6, '3/3/2012', 12.00, 'Joe'<br />
	union select 7, '3/4/2012', 10.00, 'Beth'<br />
	union select 8, '3/4/2012', 18.00, 'Sam'<br />
	union select 9, '3/4/2012', 12.00, 'Joe'<br />
	union select 10, '3/4/2012', 11.00, 'Beth'<br />
	union select 11, '3/5/2012', 14.00, 'Sam'<br />
	union select 12, '3/6/2012', 17.00, 'Beth'<br />
	union select 13, '3/6/2012', 19.00, 'Joe'<br />
	union select 14, '3/7/2012', 13.00, 'Beth'<br />
	union select 15, '3/7/2012', 16.00, 'Sam'<br />
	)<br />
select OrderID<br />
	,OrderDate<br />
	,OrderAmt<br />
	,CustomerName<br />
   from CTEOrders;</code>

# ROW_NUMBER

The ROW_NUMBER function will return a row number for each row within the partition based on the partition and order. This function requires the use of the ORDER BY clause. However, it is often used without the PARTITION BY clause as it will number the entire result set. If PARTITION BY is used, then the row numbering starts over within the partition. The following code shows how both of these work. 

<code class="codespan">select CustomerName<br />
	,OrderDate<br />
	,OrderAmt<br />
	,ROW_NUMBER() OVER(ORDER BY CustomerName) RowNumByCust<br />
	,ROW_NUMBER() OVER(PARTITION BY OrderDate ORDER BY CustomerName) RowNumPart<br />
   from CTEOrders<br />
   order by CustomerName;</code>

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/RowNumberResults2.JPG?mtime=1346987735"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/RowNumberResults2.JPG?mtime=1346987735" width="766" height="361" /></a>
</div>

# RANK and DENSE_RANK

While these are different functions with even different rules, it is easier to understand the difference when put side by side. RANK and DENSE\_RANK will order the rows based on the specified partition and apply a rank or number to them. Both RANK and DENSE\_RANK will assign the same rank to "ties". For example if rows 3 and 4 have the same value in the partition, they will have the same rank. The difference is how it handles the next rank number in the series. RANK does a "true" ordering and will apply the ranking based on the number of rows and skip numbers that are ties. So, if you have a tie between the third and fourth row and the first two rows and the final row are unique the ranking is as follows: 1, 2, 3, 3, 5. As you can see, 4 is missing. DENSE\_RANK keeps the tie as well, however it does not skip any numbers in the sequence. Here is the same example set based on using DENSE\_RANK: 1, 2, 3, 3, 4. As with the ROW_NUMBER function, the ORDER BY is required for these functions.

<code class="codespan">select CustomerName<br />
	,OrderDate<br />
	,OrderAmt<br />
	,RANK() OVER(ORDER BY CustomerName) RankByCust<br />
	,DENSE_RANK() OVER(ORDER BY CustomerName) DenseByCust<br />
	,RANK() OVER(PARTITION BY CustomerName ORDER BY OrderDate) RankByCustDt<br />
	,DENSE_RANK() OVER(PARTITION BY CustomerName ORDER BY OrderDate) DenseByCustDt<br />
   from CTEOrders<br />
   order by CustomerName;</code>

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/RankResults2.JPG?mtime=1346987733"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/RankResults2.JPG?mtime=1346987733" width="978" height="394" /></a>
</div>

# NTILE

The last of the ranking functions is NTILE. NTILE groups the data into ordered and ranked groups based on the ORDER BY clause. The number of groups used in the ranking are specified in the function itself. So if you specify four groups to produce a quartile ranking, four ranked values, one through four, will be assigned to each group based on the order. In many cases, the total numbers of rows is not divisible by the number of groups chosen. For instance, if the number of groups is 4 but the total number of rows is 39, then the first three groups will return 10 rows and the final group will only return 9 rows. The function will always frontload the results so the earliest groups will have the "extra" rows. If a PARTITION BY clause is used, the NTILE ranking will applied within each partition. As with the other ranking functions, the ORDER BY clause is required to use this function.

<code class="codespan">select CustomerName<br />
	,OrderDate<br />
	,OrderAmt<br />
	,NTILE(4) OVER(ORDER BY OrderDate) NtileByDt<br />
	,NTILE(2) OVER(PARTITION BY OrderDate ORDER BY OrderAmt) NtileByDtAmt<br />
   from CTEOrders<br />
   order by OrderDate;</code>

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/NtileResults2.jpg?mtime=1346988047"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/NtileResults2.JPG?mtime=1346988047" width="741" height="507" /></a>
</div>

Up next, using aggregate functions with window functions.

</html>