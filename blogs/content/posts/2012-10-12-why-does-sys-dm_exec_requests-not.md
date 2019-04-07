---
title: Why does sys.dm_exec_requests not show my SPID?
author: Ted Krueger (onpnt)
type: post
date: 2012-10-12T12:14:00+00:00
ID: 1754
excerpt: 'This morning, like many other mornings, I ran sys.dm_exec_requests to check for an open transaction.  This was due to an application faulting and leaving an update in an uncommitted state.  Ignoring the concept of why this happened and the transaction i&hellip;'
url: /index.php/datamgmt/dbadmin/why-does-sys-dm_exec_requests-not/
views:
  - 12343
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server

---
This morning, like many other mornings, I ran [sys.dm\_exec\_requests][1] to check for an open transaction.  This was due to an application faulting and leaving an update in an uncommitted state.  Ignoring the concept of why this happened and the transaction isn’t rolled back as typical when a connection is forcibly closed, sys.dm\_exec\_requests, introduced with the new metadata in SQL Server 2005, is usually the first DMV I run.  Usually, after I run the DMV, I yell at myself and go to others.  This is why.

sys.dm\_exec\_requests will capture any session that is active at the time you execute the DMV.  In the concept of an uncommitted transaction, this should still show in the results from sys.dm\_exec\_requests though, right?  Not always.

Example (Requires AdventureWorks – any version)

Open two SSMS query windows so you have two distinct session IDs.  In the first session, execute the following statement

sql
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE  
BEGIN TRAN  

UPDATE Sales.SalesOrderHeader SET SubTotal = 20 WHERE SalesOrderID = 49221

--COMMIT
```

Note the commented out COMMIT command.   In this example, session ID 57 is executing this UPDATE on SalesOrderHeader.  At this point, we have an uncommitted transaction.  There are a few ways we could fix this.  Close the query window telling SQL Server to roll it back, KILL the SPID, or COMMIT it.  In the case of the open transaction being left and you, as a DBA, needing to kill it so it is rolled back, you need find that session ID.  Remember, nothing can hit that SalesOrderID until this transaction is handled.  If you want to check that, go to your second query window and session ID #2 (mine is 56) and run

sql
select * from Sales.SalesOrderHeader WHERE SalesOrderID = 49221
```


You will see the SELECT never finishes as it is being blocked by session 57.

The first thing is to run sys.dm\_exec\_requests.  Or is it?  This is where the ability of sys.dm\_exec\_requests simply becomes confusing on its effectiveness.

sql
SELECT * FROM sys.dm_exec_requests where session_id = 57
```


The above result should show the active session but it does not.  Look further now at the columns that sys.dm\_exec\_requests results in.  You will see the column, open\_transaciton\_count.  OK, awesome!  This should show my open transaction count of 1.  Problem is, back to the first issue with session 57 not showing in sys.dm\_exec\_requests in the first place.

What You Should Look For

Now, what would be effective is for sys.dm\_exec\_requests to actually work the way it implies, but that isn’t the fact.  In this case, look at [DBCC OPENTRAN][2] to verify we have the open transaction we think we do.

sql
dbcc opentran
```


> Transaction information for database &#8216;AdventureWorks2012&#8217;.</p> 
> 
> Oldest active transaction:
      
> SPID (server process ID): 57
      
> UID (user ID) : -1
      
> Name : user_transaction
      
> LSN : (182:306065:1)
      
> Start time : Oct 12 2012 7:52:35:547AM
      
> SID : 0x0105000000000005150000003fad1462eb25792c07e53b2b4f610000
  
> DBCC execution completed. If DBCC printed error messages, contact your system administrator. 

results show us the open transaction and technically, our job is done and we could take care of this session.  But what if others are open?  Which one do we handle?  What we really want to look at are the DMVs [sys.dm\_tran\_database_transactions][3] and [sys.dm\_tran\_session_tranasactions][4].  This can lead us further to using [sys.dm\_exec\_connections][5] and [sys.dm\_exec\_sql_text][6] to obtain the actual statements.

 

A simplistic look at the two transaction DMVs

sql
select * from sys.dm_tran_database_transactions db_trans
   JOIN sys.dm_tran_session_transactions sessions
      ON db_trans.transaction_id = sessions.transaction_id
where database_id = 6
```

The above query should  show the open transactions in the database we need to focus on.  (AdventureWorks2012 is DBID 6 in this case).  In fact, scroll to the right and you will find another column named open\_transaction\_count.  This time, it will have a value we can utilize.  Like I mentioned earlier, this leads us deeper into the rabbit hole on what the statements being run and open really are.  Add in connections so we can grab a plan handle and return the recent statement executed.

sql
select 
	sessions.session_id,
	statements.text    
from sys.dm_tran_database_transactions db_trans
   JOIN sys.dm_tran_session_transactions sessions ON db_trans.transaction_id = sessions.transaction_id
   JOIN sys.dm_exec_connections conns ON conns.session_id = sessions.session_id
   CROSS APPLY sys.dm_exec_sql_text (conns.most_recent_sql_handle) AS statements
where database_id = 6
```

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/exec_1.gif?mtime=1350051238"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/exec_1.gif?mtime=1350051238" width="624" height="42" /></a>
</div>

As shown, we now have the open session as well as the statement run so we can fine tune the command we need to write (KILL) in order to get things moving again.

**Summary**

If you ran this example, don’t forget to COMMIT that statement for session 57.  This really shows us that each DMV has a purpose and the reason or reasons it does not show us what we may think at times.  Utilize the power all the DMVs have when they are needed.  More importantly, use them together and make sure, if one doesn’t result in what you think it should, if another DMV is more suited to the task at hand.

 [1]: http://msdn.microsoft.com/en-us/library/ms177648.aspx
 [2]: http://msdn.microsoft.com/en-us/library/ms182792.aspx
 [3]: http://msdn.microsoft.com/en-us/library/ms186957.aspx
 [4]: http://msdn.microsoft.com/en-us/library/ms188739.aspx
 [5]: http://msdn.microsoft.com/en-us/library/ms181509.aspx
 [6]: http://msdn.microsoft.com/en-us/library/ms181929.aspx