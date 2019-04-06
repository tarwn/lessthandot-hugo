---
title: Is an index seek always better or faster than an index scan?
author: SQLDenis
type: post
date: 2009-05-26T15:52:00+00:00
excerpt: |
  We all know that we should avoid index and table scans like the plague and try to get an index seek all the time.
  Okay, what will happen if we fill a table with one million rows that have the same exact value and then create a clustered index on it.
  Y&hellip;
url: /index.php/datamgmt/datadesign/is-an-index-scan-always-better-or-faster/
views:
  - 10072
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - execution plan
  - indexing
  - sql server

---
We all know that we should avoid index and table scans like the plague and try to get an index seek all the time.
  
Okay, what will happen if we fill a table with one million rows that have the same exact value and then create a clustered index on it.
  
Yes, you can create a clustered index on a non unique column because SQL Server will create a uniquefier on that column to make the index unique.

Create the following table with a clustered index

<pre>create table TestIndex (Value varchar(100) not null)

create clustered index ix_TestIndex on TestIndex(Value)</pre>

Now insert a million rows with all the same value

<pre>insert TestIndex
select top 1000000 'A' from master..spt_values s1
CROSS JOIN master..spt_values s2</pre>

Now run the following and look at the execution plan

<pre>select count(*) from TestIndex
where Value = 'A'


select count(*) from TestIndex</pre>

Here is what the plan looks like.
  


<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/ADvent/plan0.PNG?mtime=1357604160"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/ADvent/plan0.PNG?mtime=1357604160" width="654" height="279" /></a>
</div>



Here is what the plan looks like in text (you can get the output if you run SET SHOWPLAN_TEXT on first)

 _|&#8211;Compute Scalar(DEFINE:([Expr1004]=CONVERT_IMPLICIT(int,[Expr1005],0)))
         
|&#8211;Stream Aggregate(DEFINE:([Expr1005]=Count(*)))
              
|&#8211;Clustered Index Seek(OBJECT:([Test].[dbo].[TestIndex].[ix_TestIndex]),
		  
SEEK:([Test].[dbo].[TestIndex].[Value]=[@1]) ORDERED FORWARD)</p> 

|&#8211;Compute Scalar(DEFINE:([Expr1004]=CONVERT_IMPLICIT(int,[Expr1005],0)))
         
|&#8211;Stream Aggregate(DEFINE:([Expr1005]=Count(*)))
              
|&#8211;Clustered Index Scan(OBJECT:([Test].[dbo].[TestIndex].[ix_TestIndex]))
  
</em>

What about IO, is that any different?

<pre>set nocount on
set statistics io on
select count(*) from TestIndex
where Value = 'A'


select count(*) from TestIndex

set statistics io off</pre>

_Table &#8216;TestIndex&#8217;. Scan count 1, logical reads 2491, physical reads 0,
  
read-ahead reads 0, lob logical reads 0, lob physical reads 0, lob read-ahead reads 0.</p> 

Table &#8216;TestIndex&#8217;. Scan count 1, logical reads 2491, physical reads 0,
  
read-ahead reads 0, lob logical reads 0, lob physical reads 0, lob read-ahead reads 0.</em>

Looks the same to me

Now let&#8217;s take a look at the time it takes to run this

<pre>set nocount on
set statistics time on
select count(*) from TestIndex
where Value = 'A'


select count(*) from TestIndex


set statistics time off</pre>

Here is the output for that

 _SQL Server Execution Times:
     
CPU time = 110 ms, elapsed time = 115 ms.</p> 

SQL Server Execution Times:
     
CPU time = 93 ms, elapsed time = 106 ms.</em>

BTW, you will also get an index seek if you specify a WHERE clause like this

<pre>select count(*) from TestIndex
where Value &gt; ''</pre>

So there you have it, a seek is not always faster than a scan. And yes, I know that this is a silly example

Okay just for fun now let&#8217;s insert another million rows with the value B

<pre>insert TestIndex
select top 1000000 'b' from master..spt_values s1
CROSS JOIN master..spt_values s2</pre>

And now run this

<pre>select count(*) from TestIndex
where Value in( 'A','B')



select count(*) from TestIndex</pre>

You will see almost the same exact behaviour as before, a seek for the statement with the WHERE caluse and a scan for the statament without the WHERE clause.
  
The difference between before and now is that parallelism is being used as you can see in the plan below
  


<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/ADvent/Plan1.PNG?mtime=1357604252"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/ADvent/Plan1.PNG?mtime=1357604252" width="740" height="272" /></a>
</div>

Here is the text version of the execution plan

 _|&#8211;Compute Scalar(DEFINE:([Expr1004]=CONVERT_IMPLICIT(int,[globalagg1006],0)))
         
|&#8211;Stream Aggregate(DEFINE:([globalagg1006]=SUM([partialagg1005])))
              
|&#8211;Parallelism(Gather Streams)
                   
|&#8211;Stream Aggregate(DEFINE:([partialagg1005]=Count(*)))
                        
|&#8211;Clustered Index Seek(OBJECT:([Test].[dbo].[TestIndex].[ix_TestIndex]),
	  
SEEK:([Test].[dbo].[TestIndex].[Value]=&#8217;A&#8217; OR [Test].[dbo].[TestIndex].[Value]=&#8217;B&#8217;) ORDERED FORWARD)</p> 

|&#8211;Compute Scalar(DEFINE:([Expr1004]=CONVERT_IMPLICIT(int,[globalagg1006],0)))
         
|&#8211;Stream Aggregate(DEFINE:([globalagg1006]=SUM([partialagg1005])))
              
|&#8211;Parallelism(Gather Streams)
                   
|&#8211;Stream Aggregate(DEFINE:([partialagg1005]=Count(*)))
                        
|&#8211;Clustered Index Scan(OBJECT:([Test].[dbo].[TestIndex].[ix_TestIndex]))</em>

If we set the max degree of parallelism to 1 by using the MAXDOP option we get the same plan as before

<pre>select count(*) from TestIndex
where Value = 'A'
option(maxdop 1)


select count(*) from TestIndex
option(maxdop 1)</pre>

Here is the graphical plan
  


<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/ADvent/Plan2.PNG?mtime=1357604265"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/ADvent/Plan2.PNG?mtime=1357604265" width="681" height="323" /></a>
</div>

Here is the text version of the plan

_|&#8211;Compute Scalar(DEFINE:([Expr1004]=CONVERT_IMPLICIT(int,[Expr1005],0)))
         
|&#8211;Stream Aggregate(DEFINE:([Expr1005]=Count(*)))
              
|&#8211;Clustered Index Seek(OBJECT:([Test].[dbo].[TestIndex].[ix_TestIndex]),
	  
SEEK:([Test].[dbo].[TestIndex].[Value]=&#8217;A&#8217;) ORDERED FORWARD)</p> 

|&#8211;Compute Scalar(DEFINE:([Expr1004]=CONVERT_IMPLICIT(int,[Expr1007],0)))
         
|&#8211;Stream Aggregate(DEFINE:([Expr1007]=Count(*)))
              
|&#8211;Clustered Index Scan(OBJECT:([Test].[dbo].[TestIndex].[ix_TestIndex]))</em>

So there you have it, nothing earth-shattering in this post but still nice to know that a seek is not always faster than a scan



\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][2] forum**<ins></ins>

 [1]: http://forum.ltd.local/viewforum.php?f=17
 [2]: http://forum.ltd.local/viewforum.php?f=22