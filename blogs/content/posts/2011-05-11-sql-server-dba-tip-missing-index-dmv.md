---
title: SQL Server DBA Tip 12 – SQL Server Tuning – Missing Index DMV
author: Ted Krueger (onpnt)
type: post
date: 2011-05-11T10:19:00+00:00
ID: 1169
excerpt: 'Prior to SQL Server 2005, determining index needs was a much more intense process.  The use of tracing and reviewing queries running on SQL Server would have to be performed.  In SQL Server 2005 and on, DMVs have been added which make the process more e&hellip;'
url: /index.php/datamgmt/dbadmin/sql-server-dba-tip-missing-index-dmv/
views:
  - 7644
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
Prior to SQL Server 2005, determining index needs was a much more intense process.  The use of tracing and reviewing queries running on SQL Server would have to be performed.  In SQL Server 2005 and on, DMVs have been added which make the process more efficient and stable.  Determining index needs can be done with a query instead of a resource consuming process.  With these new abilities, another ugly practice arose out of SQL Server; applying all the indexes the DMVs said to apply.


** 

Missing Indexes

</strong>

Missing indexes can be found in detail by looking into the <span class="MT_green">sys.dm_db_missing_index_details</span>, <span class="MT_green">sys.dm_db_missing_index_groups</span> DMVs and <span class="MT_green">sys.dm_db_missing_index_columns</span> DMF.  These three objects are the base to retrieve the details to make observations on what SQL Server has determined is a missing index.  The example code below shows them in use.

```sql
SELECT 
	*
FROM sys.dm_db_missing_index_details AS details
CROSS APPLY sys.dm_db_missing_index_columns (details.index_handle)
INNER JOIN sys.dm_db_missing_index_groups AS groups ON groups.index_handle = details.index_handle 
```

SQL Server will populate these results without much (or any) consideration to the indexing that is already there.  This means that SQL Server will not take into account index problems that are referred to as overlapping or duplicate indexes.

To show this lets perform an example in the AdventureWorks database.

Using the Person.Address table in AdventureWorks, disable indexes IX\_Address\_AddressLine1\_AddressLine2\_City\_StateProvinceID\_PostalCode and IX\_Address\_StateProvinceID.

> <span class="MT_red">Note: To enable an index after disabling it, rebuild the index. This will enable it again</span>

Run the following query while having "show actual execution plan" set on.

```sql
SELECT 
	AddressID,
	AddressLine1,
	AddressLine2
FROM Person.Address
WHERE City = 'Seattle'
```

In the actual execution plan we can see SSMS 2008 R2 shows a suggestion for a missing index

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/All/-18.png?mtime=1305055395"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/All/-18.png?mtime=1305055395" width="624" height="119" /></a>
</div>

With the query we wrote earlier using the DMV and DMFs, we can also see the results showing the need for an index on City

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/All/-19.png?mtime=1305055395"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/All/-19.png?mtime=1305055395" width="624" height="111" /></a>
</div>

Now run the following query on the Person.Address table.

```sql
SELECT 
	AddressID,
	AddressLine1,
	AddressLine2
FROM Person.Address
WHERE City = 'Seattle'
		AND AddressLine1 LIKE '11%'
```

Reviewing the actual execution plan shows another index suggestion but now on City and AddressLine1.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/All/-20.png?mtime=1305055395"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/All/-20.png?mtime=1305055395" width="624" height="111" /></a>
</div>

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/All/-21.png?mtime=1305055395"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/All/-21.png?mtime=1305055395" width="624" height="129" /></a>
</div>

The missing index suggestion in green shown in the actual execution plan is very helpful.  However, the second image showing the missing index DMV/DMF results is misleading.  What the query is suggesting is to create an index on City and then another index on City and AddressLine1.  This is what is referred to as an overlapping index.

Down to it with the Tip

The missing index query using the DMVs and DMF we have shown is great but as discussed, may produce results that will require analysis on the reviewers' part.  In the example covered, the best course would be to create one index covering both query's requirements.  (While also placing in the covering aspects of the select statement.)

```sql
CREATE INDEX IDX_CITY_ADDY1_ASC ON Person.Address (City,AddressLine1)
INCLUDE (AddressLine2)
WITH DROP_EXISTING  
GO
```

The results of both queries will now perform Index Seek operations off the IDX\_CITY\_ADDY1_ASC index. 

Creating overlapping or duplicate indexes has a large negative impact on performance during INSERT, UPDATE and DELETE transactions.  During maintenance tasks such as fragmentation management, long runtimes due to unwanted indexes can also cause problems that should be avoided.  Optimizing any query is important.  Optimizing the use and creation of indexes themselves is just as important.

**Resources**

[Jason Strate – Index Black Ops Series/Whitepaper and more][1]

[Index DMV Usage Considerations][2]

 [1]: http://www.jasonstrate.com/2011/03/index-black-ops-series/
 [2]: /index.php/DataMgmt/DBAdmin/think-before-you-f5-on-dmvs