---
title: Finding Out How Many Times A Table Is Being Used In Ad Hoc Or Procedure Calls In SQL Server 2005 And 2008
author: SQLDenis
type: post
date: 2009-08-03T12:15:54+00:00
url: /index.php/datamgmt/dbprogramming/finding-out-how-many-times-a-table-is-be-2008/
views:
  - 30797
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
tags:
  - dmv
  - dynamic management view
  - sql server 2005
  - sql server 2008
  - tip
  - trick

---
This was asked on twitter the other day and I emailed the person the solution to this. The solution uses dynamic management views and it is not perfect because of a couple of reasons.
  
1) The dynamic management views don&#8217;t keep this information forever, restart the server and your data is gone
  
2) If your table name is in a comment it will be picked up by this query
  
3) If the table name is part of another object it will also be picked up, for example if you have a table name customer and a view name customers then it will return a row if customers was part of the query but you are lloking for the customer table

So let&#8217;s look at some code
  
First create the following stored procedure

<pre>create proc prTestProc
as
select * from master..spt_values where type = 'P'
go</pre>

Now run this query 5 times

<pre>select * from master..spt_values where type = 'P'</pre>

Run this query 6 times

<pre>select count(*) from master..spt_values where type = 'P'</pre>

Run this query 7 times

<pre>select count(*) from master..spt_values</pre>

Run this query 8 times

<pre>select count(*) from master..spt_values where type &lt;&gt; 'P'</pre>

Run this stored procedure 9 times

<pre>exec prTestProc</pre>

Now let&#8217;s look at the output. Here is the query that returns all the queries, their execution counts, if they were ad hoc or not and their last execution time. The query works by using the sys.dm\_exec\_query\_stats and sys.dm\_exec\_sql\_text dynamic management views to bring back the SQL statements themselves. 

<pre>SELECT * FROM(SELECT coalesce(object_name(s2.objectid),'Ad-Hoc') as ProcName,execution_count, 
    (SELECT TOP 1 SUBSTRING(s2.text,statement_start_offset / 2+1 , 
      ( (CASE WHEN statement_end_offset = -1 
         THEN (LEN(CONVERT(nvarchar(max),s2.text)) * 2) 
         ELSE statement_end_offset END)  - statement_start_offset) / 2+1))  AS sql_statement,
       last_execution_time
FROM sys.dm_exec_query_stats AS s1 
CROSS APPLY sys.dm_exec_sql_text(sql_handle) AS s2 ) x
where sql_statement like '%spt_values%'
AND sql_statement NOT like 'SELECT * FROM(SELECT coalesce(object_name(s2.objectid)%'
ORDER BY execution_count desc</pre>

Here is the output

<pre>ProcName	execution_count	sql_statement							last_execution_time
prTestProc	9		select * from master..spt_values where type = 'P'  		2009-08-03 10:11:38.810
Ad-Hoc		8		SELECT COUNT(*) FROM [master]..[spt_values] WHERE [type]&lt;&gt;@1	2009-08-03 10:11:22.857
Ad-Hoc		7		select count(*) from master..spt_values   			2009-08-03 10:11:19.107
Ad-Hoc		6		select count(*) from master..spt_values where type = 'P'  	2009-08-03 10:11:15.760
Ad-Hoc		5		select * from master..spt_values where type = 'P'  		2009-08-03 10:11:12.280</pre>

Let&#8217;s look at the query in more detail

This line below has the name of the table we are searching for

<pre>where sql_statement like '%spt_values%'</pre>

The line below excludes the query that we are running itself since that is not what we want to return

<pre>AND sql_statement NOT like 'SELECT * FROM(SELECT COALESCE(OBJECT_NAME(s2.objectid)%'</pre>

The line below will return Ad Hoc or the name of the object that the code was in, if s2.objectid is NULL then it was an Ad-Hoc query

<pre>coalesce(object_name(s2.objectid),'Ad Hoc')</pre>

As you can see this is probably good enough to give you some quick results to find out if a table is used so that you can drop it. The way I do this is I rename the table by prefixing it with 2 underscores, this enables two things for me
  
1) I can quickly find the table since it will be on top in the object explorer
  
2) I don&#8217;t have to think what the original name was, I just remove the 2 leading underscores

Of course you can also run a trace and then store that in a file, this enables you then to parse the file with a trace tool (more on that later this week when I will blog about a couple of free trace tools, Ami Levin just notified me about a new free trace tool so I will include that one). You could also use Extended Events to do something like this but I like this approach because it is very simple and you can all do it in T-SQL



\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][2] forum**<ins></ins>

 [1]: http://forum.lessthandot.com/viewforum.php?f=17
 [2]: http://forum.lessthandot.com/viewforum.php?f=22