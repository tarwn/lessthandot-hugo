---
title: 'SQL Advent 2012 Day 15: Benefits of Indexes'
author: SQLDenis
type: post
date: 2012-12-15T07:30:00+00:00
ID: 1856
excerpt: This is day fifteen of the SQL Advent 2012 series of blog posts. Today we are going to look at indexes
url: /index.php/datamgmt/dbprogramming/benefits-of-indexes/
views:
  - 9408
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - indexing
  - performance
  - query
  - sql advent 2012
  - sql server 2008
  - sql server 2008 r2
  - sql server 2012

---
This is day fifteen of the [SQL Advent 2012 series][1] of blog posts. Today we are going to look at the benefit of indexes

## So how does an index work?

How does an index work, how does it help SQL Server finding stuff faster? Here is an simple non technology explanation. If I told you to grab a cookbook and give me all the recipes in that book for cod, what would you do? There are two things yuo can do, you can read through the whole book page by page until you get to the last page looking for any cod recipes. Or………..take a look at the picture below

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/ADvent/IMG01005-20121214-1059.jpg?mtime=1355500346"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/ADvent/IMG01005-20121214-1059.jpg?mtime=1355500346" width="640" height="480" /></a>
</div>

See that, in one second I know exactly where to find cod recipes, it is on page 305 and 61. Which do you think is faster, looking it up in an index or scanning through the book? 

SQL Server pretty much uses the same technique. There are two types of _basic_ indexes in SQL Server, these are clustered and non clustered indexes. A clustered index will contain all the data from the table as well, a non clustered index will have a pointer to the table row if there is no clustered index on that table or to the clustered index position is there is a clustered index on the table.

A table with a clustered index is also called a clustered table. A table without a clustered index is also called a heap.

SQL Server also has XML, spatial, columnstore, filtered, full text indexes but we are just focusing on the basic indexes in this post.

Here is what books on line has to say about the [storage of a clustered index][2]

> _In SQL Server, indexes are organized as B-trees. Each page in an index B-tree is called an index node. The top node of the B-tree is called the root node. The bottom level of nodes in the index is called the leaf nodes. Any index levels between the root and the leaf nodes are collectively known as intermediate levels. In a clustered index, the leaf nodes contain the data pages of the underlying table. The root and intermediate level nodes contain index pages holding index rows. Each index row contains a key value and a pointer to either an intermediate level page in the B-tree, or a data row in the leaf level of the index. The pages in each level of the index are linked in a doubly-linked list._

A non clustered index is a little different since it doesn't store the whole data pages, here is what books on line has to say about [the storage of a nonclustered index][3]

> _Nonclustered indexes have the same B-tree structure as clustered indexes, except for the following significant differences:</p> 
> 
>   * The data rows of the underlying table are not sorted and stored in order based on their nonclustered keys.
>   * The leaf layer of a nonclustered index is made up of index pages instead of data pages.
> 
> Nonclustered indexes can be defined on a table or view with a clustered index or a heap. Each index row in the nonclustered index contains the nonclustered key value and a row locator. This locator points to the data row in the clustered index or heap having the key value.
  
> The row locators in nonclustered index rows are either a pointer to a row or are a clustered index key for a row, as described in the following:
> 
>   * If the table is a heap, which means it does not have a clustered index, the row locator is a pointer to the row. The pointer is built from the file identifier (ID), page number, and number of the row on the page. The whole pointer is known as a Row ID (RID).
>   * If the table has a clustered index, or the index is on an indexed view, the row locator is the clustered index key for the row. If the clustered index is not a unique index, SQL Server makes any duplicate keys unique by adding an internally generated value called a uniqueifier. This four-byte value is not visible to users. It is only added when required to make the clustered key unique for use in nonclustered indexes. SQL Server retrieves the data row by searching the clustered index using the clustered index key stored in the leaf row of the nonclustered index.
> 
> </em> </blockquote> 
> 
> Cool, I will now just add indexes on every column. Not so fast, there are some things to consider here, for every update, insert and delete statement indexes have to be maintained. If you have a busy OLTP database, you have to find the right balance between your read and write IO. Not enough indexes and your retrieval queries will suffer, too many indexes and your inserts will be slower. Also keep in mind that indexes take up storage, the more you have the bigger your database will be.
> 
> ## Keep your clustered indexes narrow
> 
> Try to keep your clustered indexes as narrow as possible, if you can use something like an integer, this is only 4 bytes. The reason to keep your clustered indexes narrow is that when you have non clustered indexes, the row locator is the clustered index key for the row. In this case your non clustered index will become bigger as well and now you won't be able to store as much data on a page. To illustrate that let's take a look at some simple code
> 
> First let's create this table and populate it with 2048 rows
> 
> sql
CREATE TABLE Test1(id int, somecol char(36), somecol2 char(36))
> GO
> 
> INSERT Test1 
> SELECT number,newid(),newid() 
> FROM master..spt_values
> WHERE type = 'P'
```

> Add a clustered index
> 
> sql
CREATE CLUSTERED INDEX cx on Test1(id)
```

> Add these two non clustered indexes
> 
> sql
CREATE NONCLUSTERED INDEX ix1 on Test1(somecol)
> CREATE NONCLUSTERED INDEX ix2 on Test1(somecol2)
```

> Let's check how much storage is required for the non clustered indexes
> 
> sql
SELECT
> DB_NAME(DATABASE_ID) AS [DatabaseName],
> OBJECT_NAME(OBJECT_ID) AS TableName,
> SI.NAME AS IndexName,
> INDEX_TYPE_DESC AS IndexType,
> PAGE_COUNT AS PageCounts
> FROM sys.dm_db_index_physical_stats (DB_ID(), NULL, NULL , NULL, N'LIMITED') DPS
> INNER JOIN sysindexes SI
> ON DPS.OBJECT_ID = SI.ID AND DPS.INDEX_ID = SI.INDID
> AND OBJECT_NAME(OBJECT_ID) = 'Test1'
> GO
```

> Here is the output, as you can see the non clustered indexes take up 12 pages
> 
> <pre>DatabaseName	TableName	IndexName	IndexType	PageCounts
tempdb	        Test1	        cx	      CLUSTERED INDEX	22
tempdb	        Test1	        ix1	   NONCLUSTERED INDEX	12
tempdb	        Test1	        ix2	   NONCLUSTERED INDEX	12</pre>
> 
> If we check the table size
> 
> sql
EXEC sp_spaceused 'Test1'
```

> <pre>name	rows	reserved	data	index_size	unused
Test1	2048    472 KB	       176 KB	240 KB	        56 KB</pre>
> 
> We see that it is using 240 KB for the indexes
> 
> Let's recreate the clustered index with all 3 columns now.
> 
> sql
CREATE CLUSTERED INDEX cx on Test1(id,somecol,somecol2)
> WITH DROP_EXISTING
```

> Recreating the clustered index also recreated the non clustered indexes. Let's check now how many pages a non clustered index is
> 
> sql
SELECT
> DB_NAME(DATABASE_ID) AS [DatabaseName],
> OBJECT_NAME(OBJECT_ID) AS TableName,
> SI.NAME AS IndexName,
> INDEX_TYPE_DESC AS IndexType,
> PAGE_COUNT AS PageCounts
> FROM sys.dm_db_index_physical_stats (DB_ID(), NULL, NULL , NULL, N'LIMITED') DPS
> INNER JOIN sysindexes SI
> ON DPS.OBJECT_ID = SI.ID AND DPS.INDEX_ID = SI.INDID
> AND OBJECT_NAME(OBJECT_ID) = 'Test1'
> GO
```

> Here are the results
> 
> <pre>DatabaseName	TableName	IndexName	IndexType	PageCounts
tempdb          Test1	        cx	        CLUSTERED INDEX	22
tempdb          Test1	        ix1	     NONCLUSTERED INDEX	21
tempdb  	Test1	        ix2	     NONCLUSTERED INDEX	21</pre>
> 
> As you can see the non clustered indexes went from 12 to 21 pages
> 
> The index size changed, if you run this
> 
> sql
EXEC sp_spaceused 'Test1'
```

> Here is the result
> 
> <pre>name	rows	reserved data	index_size	unused
Test1	2048    600 KB	 176 KB	384 KB	        40 KB</pre>
> 
> So we went from 240 KB to 384 KB for the index storage.
> 
> So why does this matter you ask? SQL Server will use indexes for all kind of things, if you run a COUNT(*) it will use an index, if you do a JOIN it will use an index, it will use indexes in GROUP By queries and many more things.
> 
> Let's look at a simple example, when you do a COUNT(*), the optimizer will pick a non clustered index if there is one since it usually has less columns than the clustered index 
> 
> sql
SET SHOWPLAN_TEXT ON
> GO
> 
> SELECT count(*) FROM Test1
> GO
> 
> SET SHOWPLAN_TEXT OFF
> GO
```

> Here is the plan
> 
> > |&#8211;Compute Scalar(DEFINE:([Expr1004]=CONVERT_IMPLICIT(int,[Expr1005],0)))
         
> > |&#8211;Stream Aggregate(DEFINE:([Expr1005]=Count(*)))
              
> > |&#8211;Index Scan(OBJECT:([ReportServer].[dbo].[Test1].[ix2]))
> 
> Basically it had to scan through all the index pages to get the count, if your index was now still 12 pages instead of 21, SQL Server would take less time to accomplish this.
> 
> I barely scratched the surface on indexing, it is a big topic and I recommend you start by navigating to the topic in Books On Line: http://msdn.microsoft.com/en-us/library/ms175049.aspx
> 
> You can also take a look at the following posts written about indexing right here on this site
> 
> [Index REBUILD and REORGANIZE][4]
  
> [Indexes with Included Columns][5]
  
> [Filtered Indexes][6]
  
> [Is an index seek always better or faster than an index scan?][7]
  
> [Finding Fragmentation Of An Index And Fixing It][8]
  
> [How to get the selectivity of an index][9]
  
> [Adding nonclustered index on primary keys][10]
  
> [Index Seek on LOB Columns][11]
  
> [Row Overflow Pages &#8211; Index Tuning][12]
  
> [Columnstore Index Basics][13]
  
> [Columnstore Index – Index Statistics][14]
> 
> That is all for day fifteen of the [SQL Advent 2012 series][1], come back tomorrow for the next one, you can also check out all the posts from last year here: [SQL Advent 2011 Recap][15]

 [1]: /index.php/DataMgmt/DBProgramming/sql-advent-2012-here-is
 [2]: http://msdn.microsoft.com/en-us/library/ms177443(v=SQL.105).aspxhttp://msdn.microsoft.com/en-us/library/ms177443(v=SQL.105).aspx
 [3]: http://msdn.microsoft.com/en-us/library/ms177484(v=sql.105).aspx
 [4]: /index.php/DataMgmt/DataDesign/index-rebuild-and-reorganize-sql
 [5]: /index.php/DataMgmt/DataDesign/sql-advent-2011-day-20
 [6]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/sql-advent-2011-day-19
 [7]: /index.php/DataMgmt/DataDesign/is-an-index-scan-always-better-or-faster
 [8]: /index.php/DataMgmt/DataDesign/finding-fragmentation-of-an-index-and-fi/index.php/DataMgmt/DataDesign/finding-fragmentation-of-an-index-and-fi
 [9]: /index.php/DataMgmt/DataDesign/how-to-get-the-selectivity-of-an-index/index.php/DataMgmt/DataDesign/how-to-get-the-selectivity-of-an-index
 [10]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/adding-nonclustered-index-on-primary
 [11]: /index.php/DataMgmt/DBAdmin/index-seek-on-lob-columns
 [12]: /index.php/DataMgmt/DBAdmin/performance-impact-of-row-overflow
 [13]: /index.php/DataMgmt/DBAdmin/columnstore-index-basics
 [14]: /index.php/DataMgmt/DBAdmin/columnstore-index-index-statistics
 [15]: /index.php/DataMgmt/DataDesign/sql-advent-2011-recap