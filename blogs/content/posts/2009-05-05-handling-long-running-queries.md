---
title: Handling long running queries with a stop sign
author: Ted Krueger (onpnt)
type: post
date: 2009-05-05T11:14:28+00:00
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/handling-long-running-queries/
views:
  - 11092
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server Admin

---
Handling long running queries on SQL Server can be one of the hardest things to deal with as a DBA. You have dozens of variables that must be considered before you can actually do anything about them. Usually if you ask a number of DBAs how they go about determining if the long runtimes can be resolved, you will get several different variations of troubleshooting. Even if the steps are blurred in sequential order, they will typical contain a few of the following

Execution Plan analysis
  
Indexing options
  
Disk alterations
  
Data storage alterations
  
Latency troubleshooting
  
Application issues
  
resource issues (Memory etc&#8230;)
  
and the list goes on for awhile. 

Sometimes you come to a quick resolution and sometimes the resolution is a large scale restructure that just cannot be completed given resources and time. Good examples of this are queries that do massive amounts of reads on historical tables. This is commonly pushed into your typical data warehouse solution but in some cases and some ERP solutions, the applications will allow the users to select reporting criteria that is less than favorable.

The option that you can use in this case is the query governor. Before I go into this configuration I&#8217;d like to warn you about it. The query governor uses the estimated execution plan. This is critical to you and setting this option because if statistics are bad or you have a cached plan that isn&#8217;t favorable, you are going to get bad results from the governor and it determining if the query realistically will take greater than the set value. I would recommend in the last resort cry for help you make by setting this option, you update all statistics, remove fragmentation and then clear the cache on the instance. Now retest the reason you were even looking at this option and see if it is still required. Of course after you re-cache the plan 😉

If it comes to the query governor being configured then there is another concern you have to be aware of. It will not catch your dumb mistakes! I mean that literally.

I&#8217;ll show you want I mean in configuring the governor.

The governor has two options on the context in which you can configure it. Either you can set the server wide governor or per transaction. I won&#8217;t go into this much sense we have the awesome documentation over in BOL. You can find that here [query governor cost limit Option][1]

Important things to point out are the fact the value you attempt to set the governor to is truncated to int. So that means you don&#8217;t get Mr. Run less than 9.43214365 seconds. Also be aware of the security restrictions for configuring this.

So let&#8217;s do it.
  
First open a new query window and execute

<pre>sp_configure 'show advanced options', 1;
GO
RECONFIGURE;
GO
sp_configure  'query governor cost limit' ,1;
GO
RECONFIGURE;
GO</pre>

Now attempt to query a large table or even a poorly indexed table that you know iwll take larger than a second to return. You should be shown the following
  
Msg 8649, Level 17, State 1, Line 1
  
The query has been canceled because the estimated cost of this query (494) exceeds the configured threshold of 1. Contact the system administrator.

In another blog I&#8217;ll go into playing with these error messages returned to make them user friendly. 

So you&#8217;ve successfully kept your database server from having the life sucked out of it from one bad query. Or have you?

Run this now while you still have the configuration at 1

<pre>While 1=1
 Begin
  Select 'Oh boy...'
 End</pre>

Alright, this was a given what was going to happen. It&#8217;s an infinite loop so it&#8217;s going to run forever. Given a really bad loop you will proceed down the path of sucking the same life out of the server the earlier query may have. This is a misconception of the query governor though. The reason for that is of course the execution plan. If you throw the select on the large table in there, then the governor will pick it up due to the execution plan determining the internal select.

Now configure the governor back to 0 (default and means forever). What you can do now is try it in your session with the SET. 

<pre>SET QUERY_GOVERNOR_COST_LIMIT 1
SELECT * FROM BIGARCETABLE
SET QUERY_GOVERNOR_COST_LIMIT 0</pre>

Makes you feel dirty even thinking you would want to do that doesn&#8217;t it. It is a good warning though if you are possibly missing a DBA position in the company or are running time statistics on how long a query is averaging over time based on performance deterioration from transactions going on in the background. Fragmentation levels can quickly cause a query to go from milliseconds to seconds and that can cause a severe problem with some business tasks. Saying that I do want to say I&#8217;ve only used this option in those few situations. Setting this server wide is tricky and if you do attempt to make sure you are maintaining your objects very well before you do.

Don&#8217;t get this confused with the enterprise feature in SQL Server 2008 called the resource governor either. The resource governor can stop/limit long running queries and limit resources allocated to them along with several other configurations and monitoring features. It is completely different to the query governor though and much more of a feature than a configuration.

 [1]: http://msdn.microsoft.com/en-us/library/ms190419.aspx