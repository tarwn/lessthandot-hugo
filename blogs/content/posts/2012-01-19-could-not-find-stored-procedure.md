---
title: Could not find stored procedure ‘sp_ExecuteSQL’
author: Ted Krueger (onpnt)
type: post
date: 2012-01-19T23:23:00+00:00
ID: 1499
excerpt: |
  Tonight I was upgrading some SSIS packages for another blog on automating index statistics. While doing this task, I ran into the error, "Could not find stored procedure 'sp_ExecuteSQL'".  After sitting there, in disbelief that sp_executesql wasn’t on t&hellip;
url: /index.php/datamgmt/dbprogramming/could-not-find-stored-procedure/
views:
  - 17755
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server Admin

---
Tonight I was upgrading some SSIS packages for another blog on automating index statistics. While doing this task, I ran into the error, &#8220;Could not find stored procedure &#8216;sp\_ExecuteSQL'&#8221;.  After sitting there, in disbelief that sp\_executesql wasn’t on the SQL Server 2012 RC0 instance, I realized the problem was me.  I actually just gave the answer to what the issue was.

The database that I was testing on was AdventureWorks.  AdventureWorks has a collation setting of Latin1\_General\_100\_CS\_AS.  Latin1\_General\_100\_CS\_AS is a case sensitive collation, unlike a more common collation of SQL\_Latin1\_General\_CP1\_CI\_AS.  So sp\_ExecuteSQL is not a valid name.  sp_executesql is the actual name of the system procedure and the correct case, all lower case.

As a quick test, in your own AdventureWorks database, run the following

sql
EXEC sp_ExecuteSQL N'SELECT * FROM sys.databases'
```


resulting in the following error

<span class="MT_red">Msg 2812, Level 16, State 62, Line 1<br /> Could not find stored procedure &#8216;sp_ExecuteSQL&#8217;</span>.

The correct syntax would be

sql
EXEC sp_executesql N'SELECT * FROM sys.databases'
```


The fix is simply changing the name to be correct, case wise.  The tip that can come from this is, as a DBA, we become used to typing a specific procedure, a specific way on a set of databases configured a specific way. However, we need to take into account that even system procedures will take the collation of the database they are being executed in. Becoming comfortable and letting your guard down while making assumptions of what we are working on can cause problems while writing them, or worse, after they are in a production environment.

 

Happy DBAing!