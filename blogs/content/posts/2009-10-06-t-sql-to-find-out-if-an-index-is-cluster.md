---
title: T-SQL To find Out If An Index Is Clustered Or Non Clustered
author: SQLDenis
type: post
date: 2009-10-06T16:41:12+00:00
url: /index.php/datamgmt/datadesign/t-sql-to-find-out-if-an-index-is-cluster/
views:
  - 10516
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - functions
  - how to
  - indexing
  - sql server 2000
  - sql server 2005
  - sql server 2008
  - tip

---
I saw this question in in Google Analytics from a Google search that hit our site.
  
So, how can you determice if an index is clustered or not? There are two ways, you can use either the INDEXPROPERTY function or the sysindexes/sys.sysindexes system table/view

To see how it works we will create a table with one clustered and one non clustered index on it
  
Here is the code for that

<pre>USE tempdb
go
 
CREATE TABLE Test (id INT, col1 VARCHAR(40), col2 INT, col3 INT)
go


CREATE NONCLUSTERED  INDEX ix_test ON test (id ASC, col1 DESC,col2 DESC, col3 ASC)
go
 
 
CREATE CLUSTERED  INDEX ix_test_clust ON test (id ASC, col1 DESC)
go</pre>

**INDEXPROPERTY**
  
To use INDEXPROPERTY you need to know the table ID, the name of the index and use IsClustered for the property. To get the table id, you use the OBJECT_ID function with the table name passed in
  
So for the index ix\_test on table Test we will use INDEXPROPERTY(OBJECT\_ID(&#8216;Test&#8217;), &#8216;ix_test&#8217;,&#8217;IsClustered&#8217;)

<pre>SELECT 'ix_test',INDEXPROPERTY(OBJECT_ID('Test'), 'ix_test','IsClustered')
union all
SELECT 'ix_test_clust',INDEXPROPERTY(OBJECT_ID('Test'), 'ix_test_clust', 'IsClustered')</pre>



<pre>Output
-------------------
ix_test		0
ix_test_clust	1</pre>

**SYSINDEXES**
  
indid holds the information needed to determine if the index is clustered or not
  
This info is for SQL Server 2000
  
_1 = Clustered index
  
>1 = Nonclustered
  
255 = Entry for tables that have text or image data_

This info is for SQL Server 2005 and up

_0 = Heap
  
1 = Clustered index
  
>1 = Nonclustered index_

<pre>select name,case indid when 1 then 'Clustered' else 'Non Clustered' end as TypeOfIndex
from sysindexes --or sys.sysindexes on sql 2005 and up
where name in( 'ix_test', 'ix_test_clust')</pre>



<pre>Output
-----------------------
name	TypeOfIndex
ix_test_clust	Clustered
ix_test	Non Clustered</pre>

And of course you can combine the two methods

<pre>SELECT name,INDEXPROPERTY(id, name,'IsClustered')
from sysindexes 
where name in( 'ix_test', 'ix_test_clust')</pre>



<pre>Output
-------------
ix_test_clust	1
ix_test		0</pre>

As you can see it is pretty easy to determine if an index is a clustered index or a non clustered index. I prefer to use INDEXPROPERTY instead of SYSINDEXES, what about you?



\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][2] forum**<ins></ins>

 [1]: http://forum.ltd.local/viewforum.php?f=17
 [2]: http://forum.ltd.local/viewforum.php?f=22