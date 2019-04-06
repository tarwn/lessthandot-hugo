---
title: Do you use disable index or drop index when running your ETL processes in SQL Server
author: SQLDenis
type: post
date: 2010-04-21T15:32:40+00:00
url: /index.php/datamgmt/dbprogramming/do-you-use-disable-index-or-drop-index-w/
views:
  - 12734
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - indexing
  - question

---
There are certain operations where dropping an index, loading data and then again creating the index can speed up data loading. SQL server 2005 introduced a way to disable an index.

Let&#8217;s take a look, first create this table

<pre>Create table TestIndex (id int, somecol varchar(20))</pre>

Insert a little bit of data

<pre>insert into TestIndex
  select number,CONVERT(varchar(20),getdate(),100)
  from master..spt_values
  where type = 'p'</pre>

Create a nonclustered index

<pre>create index ix_TestIndex on TestIndex(id,somecol)</pre>

Now let&#8217;s disable this index

<pre>ALTER INDEX ix_TestIndex
  ON TestIndex
  DISABLE</pre>

Now when we run our query against the table and look at the plan we get a table scan

<pre>set showplan_text on
  go
  select * from TestIndex
  go
  set showplan_text off
  go</pre>

<pre>|--Table Scan(OBJECT:([master].[dbo].[TestIndex]))
    
    </pre>

Now let&#8217;s rebuild the index again

<pre>ALTER INDEX ix_TestIndex
  ON TestIndex
  REBUILD</pre>

Now we will run the same query again

<pre>set showplan_text on
  go
  select * from TestIndex
  go
  set showplan_text off
  go</pre>

<pre>|--Index Scan(OBJECT:([master].[dbo].[TestIndex].[ix_TestIndex]))
    </pre>

As you can see, it uses the index again 

Now let&#8217;s drop this index

<pre>drop index TestIndex.ix_TestIndex</pre>

**Is there a difference how disable works between nonclustered and clustered indexes?**
  
Let&#8217;s take a look, first create this clustered index

<pre>create clustered index ix_TestIndexClustered on TestIndex(id,somecol)</pre>

Now let&#8217;s disable this clustered index

<pre>ALTER INDEX ix_TestIndexClustered
  ON TestIndex
  DISABLE</pre>

And now when we run the query from before

<pre>set showplan_text on
  go
  select * from TestIndex
  go
  set showplan_text off
  go
  </pre>

We get this error
  
 _Msg 8655, Level 16, State 1, Line 1
  
The query processor is unable to produce a plan because the index &#8216;ix_TestIndexClustered&#8217; on table or view &#8216;TestIndex&#8217; is disabled._

As you can see while a clustered index is disabled the data is unavailable. Not only that, you can also not insert anything into the table,
  
So this query

<pre>insert into TestIndex
select 2,'Bla'</pre>

Fails with the same error from before
  
_Msg 8655, Level 16, State 1, Line 1
  
The query processor is unable to produce a plan because the index &#8216;ix_TestIndexClustered&#8217; on table or view &#8216;TestIndex&#8217; is disabled._

If we rebuild the clustered index again

<pre>ALTER INDEX ix_TestIndexClustered
  ON TestIndex
  REBUILD</pre>

And if we run this query again

<pre>set showplan_text on
  go
  select * from TestIndex
  go
  set showplan_text off
  go</pre>

<pre>|--Clustered Index Scan(OBJECT:([master].[dbo].[TestIndex].[ix_TestIndexClustered]))</pre>

We can see that it does use the clustered index

**My question to you.**
  
So my question to you people is, do any of you use this instead of drop and create index? One advantage I see is that you don&#8217;t need to update the code that drops and recreates the non clustered index if your index definition changes when using disable index in your ETL process. If you disable a clustered index you can also not insert into the table.

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][2] forum**<ins></ins>

 [1]: http://forum.lessthandot.com/viewforum.php?f=17
 [2]: http://forum.lessthandot.com/viewforum.php?f=22