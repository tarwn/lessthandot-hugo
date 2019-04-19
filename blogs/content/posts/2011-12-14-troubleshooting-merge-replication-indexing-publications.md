---
title: Troubleshooting Merge Replication Performance – Indexing Publications
author: Ted Krueger (onpnt)
type: post
date: 2011-12-14T11:25:00+00:00
ID: 1431
excerpt: |
  Troubleshooting Merge Replication – Index your publication design
  When setting up and designing a merge replication publication that will utilize join filters, indexing strategies can be vital to determining design issues in the setup.  Take the below&hellip;
url: /index.php/datamgmt/dbadmin/troubleshooting-merge-replication-indexing-publications/
views:
  - 5832
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
When setting up and designing a merge replication publication that will utilize join filters, indexing strategies can be vital to determining design issues in the setup.  Take the below query on the AdventureWorks database.

```sql
SELECT 
	<columns>
FROM Person.Person
JOIN Sales.SalesOrderHeader ON Person.BusinessEntityID = SalesOrderHeader.SalesPersonID
JOIN Sales.SalesOrderDetail ON [SalesOrderHeader].[SalesOrderID] = [SalesOrderDetail].[SalesOrderID]
WHERE Person.LoginAccount = SUSER_SNAME()
```

The query above is a common query that is the result of designing merge replication with parameterized filters and join filters.  It filters down as a parameterized filter on the system function SUSER\_SNAME() and then uses join filters on SalesOrderHeader and SalesOrderDetail.  This design will allow for the subscriber to this query, or publication, to only retrieve data that is matching the predicate of their SUSER\_SNAME().  The value here can be seen in a publishing database that may be 500GB and the subscribers only have 200MB of data that they need to continue functioning in the business entity they belong to.

In merge replication, the query shown above is shown in the publication properties below

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-87.png?mtime=1323567447"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-87.png?mtime=1323567447" width="382" height="343" /></a>
</div>

As shown, the filters are built the same way as the query.  The table Person.Person is the predicate on SUSER_SNAME() or in replication terms, the parameterized filter.  The first join filter on Sales.SalesOrderHeader is the same as the INNER JOIN in the query as well as the second INNER JOIN being the second join filter on Sales.SalesOrderDetail.

This technique of writing a query can assist in designing merge and transactional publications.  It can also allow for a critical tuning stage of replication designs.  Replication of any type is still based on SQL Server querying data.  This querying of the data is based off the properties configured such as filters.  To tune this publication for optimal subscribing, you would then tune the query you are mimicking.

In the article tuning for [parameterized filters][1], the index IDX\_PARTITION\_LOGIN\_GUID\_ASC was created.  This same index will help in the publication created for this article.  The definition of the index is shown below.

```sql
CREATE NONCLUSTERED INDEX [IDX_PARTITION_LOGIN_GUID_ASC]
ON [Person].[Person] ([LoginAccount])
INCLUDE ([rowguid])
GO
```

To show the tuning steps, the plan below is a plan based off the query shown early but without the IDX\_PARTITION\_LOGIN\_GUID\_ASC index.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-88.png?mtime=1323567447"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-88.png?mtime=1323567447" width="624" height="207" /></a>
</div>

The IO Statistics that we generate from this query

<span style="font-size: x-small;"><span class="MT_red"><br /> Table 'SalesOrderDetail'. Scan count 450, logical reads 1545, physical reads 0, read-ahead reads 0, lob logical reads 0, lob physical reads 0, lob read-ahead reads 0.<br /> Table 'SalesOrderHeader'. Scan count 1, logical reads 1390, physical reads 0, read-ahead reads 0, lob logical reads 0, lob physical reads 0, lob read-ahead reads 0.<br /> Table 'Person'. Scan count 1, logical reads 3816, physical reads 0, read-ahead reads 0, lob logical reads 0, lob physical reads 0, lob read-ahead reads 0.<br /> </span></span>

The index on LoginAccount is already required due to the parameterized filter.  Add this index back so the predicate usage of LoginAccount prevents poor performance.  Refer back to [parameterized filters][1] for more details on this tuning method.

Here is a look at the plan a select on all the columns returns after the IDX\_PARTITION\_LOGIN\_GUID\_ASC index is created.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-89.png?mtime=1323567448"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-89.png?mtime=1323567448" width="531" height="270" /></a>
</div>

From reviewing the IO statistics and the execution plan, we have altered the previous index scan on the Person table.  However, we have also introduced a Key Lookup operation.  This is due to the lack of a covering index.  A covering index refers to an index covering all the columns that are used in a query's results as well as any predicates used.  In other terms, the index satisfies all the column needs of the query.

In reality, and replication terms, you want to only replicate the columns you require but it is common that all columns are required.

In AdventureWorks, the first indexing strategy is to ensure the JOIN conditions are indexed.  include the BusinessEntityID, SalesPersonID and both SalesOrderID columns.  Ensuring join keys are indexed will not show as a resulting _seek operation_ in a plan.  So knowing to ensure these conditions are also indexed so the joins themselves are optimal is, in most cases, critical to performance.  Since the parameterized filter was introduced already, this publication will be tuned from additional join filtered specifications.

BusinessEntityID is a primary key on Person.Person so there is no need to alter this.  However, what about the covering index strategy?  Note that the plan shown above has two Key Lookup operations.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-90.png?mtime=1323567448"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-90.png?mtime=1323567448" width="800" height="141" /></a>
</div>

For Person.Person, the table is very small, both in width and rows.  This means the Key lookup on this table will not be a large hit on performance.  The Key Lookup performance being less of a performance hit can be seen quickly in the IO Statistics.

<span style="font-size: x-small;"><span class="MT_red">Table 'Person'. Scan count 1, logical reads 5, physical reads 0, read-ahead reads 0, lob logical reads 0, lob physical reads 0, lob read-ahead reads 0.</span></span>

What happens if your company buys another company and triples the head count though?  With this planning, an index may be a good idea.  What should be considered however is the number of indexes on the table already.  As more indexes are created, the possibility for INSERT, UPDATE and DELETE transactions to be slower increases.  There are also effects on maintenance.  More indexes means longer and more resource-intensive maintenance tasks.  Take all these pieces into account when planning indexing.  For this case, the Key Lookup will be resolvee with a change to the existing index, IDX\_PARTITION\_LOGIN\_GUID\_ASC that was created earlier to support the parameterized filter on LoginAccount.

In order to fix the Key Lookups, all the columns in the table should be included as INCLUDE columns.  This is required due to the article itself consisting of each column.  Once the columns are in the INCLUDE, it will create a covering index.  Normally, creating an index with all the columns is frowned upon.  For replication and even really poor queries, at times, it is needed.  For Person.Person this is true based on the predicate of LoginAccount being the parameterized filter.  In most cases, the primary key is the join condition and can offer efficient means for returning all the columns.  Remember, the primary key as a clustered index is the table.  Other indexing and table design make this vary greatly though. Work the tuning strategy into your own designs as if you were tuning any query for optimal performance.

Adding the included columns to the index on LoginAccount shows the first Key Lookup operation resolved.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-91.png?mtime=1323567448"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-91.png?mtime=1323567448" width="465" height="255" /></a>
</div>

To resolve the Key Lookup that is already causing a performance problem on SalesOrderHeader, we can alter a pre-existing nonclustered index in AdventureWorks, [IX\_SalesOrderHeader\_SalesPersonID].

This index is on SalesOrderID but without include columns defined.  Add the remaining columns in the SalesOrderHeader to the nonclustered index and apply the changes to the index.

Rerun the estimated plan and review the new plan from the changes to the indexing.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-92.png?mtime=1323567449"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-92.png?mtime=1323567449" width="624" height="186" /></a>
</div>

 

**Conclusion**

This tuning exercise saved a total of only 50 milliseconds on the query tuned.  Prior execution was on average 820 milliseconds and the tuned plan results in as average of 780.   This change may not be worth the performance impact it has on the other indexing.  This includes impact on INSERT, UPDATE and DELETE transactions, lowered CPU by lowering overall IO and more efficient maintenance operations.

 [1]: /index.php/DataMgmt/DBAdmin/merge-replication-parameterized-filters