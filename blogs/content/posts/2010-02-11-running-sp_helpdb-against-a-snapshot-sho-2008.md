---
title: Running sp_helpdb against a snapshot shows wrong files on SQL Server 2008
author: SQLDenis
type: post
date: 2010-02-11T12:58:49+00:00
url: /index.php/datamgmt/dbprogramming/running-sp_helpdb-against-a-snapshot-sho-2008/
views:
  - 5740
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - bug
  - feature
  - snapshot
  - sql server 2008

---
If you are using snapshots be aware that running sp_helpdb reports wrong files. Let&#8217;s take a look by running some code

First create a test database

<pre>use master
go

CREATE DATABASE [test] ON  PRIMARY 
( NAME = N'test', FILENAME = N'C:test.mdf'  )
 LOG ON 
( NAME = N'test_log', FILENAME = N'C:test_log.LDF' )

GO</pre>

Now let&#8217;s create a table and populate it with some data

<pre>USE test
go


create table TestTable (id int not null,somecol char(100) default 'a')
go

insert TestTable(id)
select row_number() over (order by s1.id)
from sysobjects s1
cross join sysobjects s2

go</pre>

Now create the snapshot database

<pre>use master
go

    
    CREATE DATABASE TestSnapshot ON  
( NAME = N'test', FILENAME = N'C:testss.mdf' )
  AS SNAPSHOT of Test;
GO</pre>

Now run a count for that table against the test database and against the snapshot

<pre>select COUNT(*) from TestSnapshot..TestTable
select COUNT(*) from Test..TestTable</pre>

<pre>Output
--------------
3025
3025</pre>

As you can see the count is the same

Now run sp_helpdb and look at the sizes, we will come back to this output later

<pre>sp_helpdb 'Test'</pre>

<pre>Output
---------------------------------------------------------------
test		1	C:test.mdf		PRIMARY	2304 KB
test_log	2	C:test_log.LDF	        NULL	1792 KB</pre>

Let&#8217;s add some more data to our table

<pre>use Test
go

declare @id int =(select MAX(id)
					from  Test..TestTable)

insert Test..TestTable(id)
select row_number() over (order by s1.id) + @id
from sysobjects s1
cross join sysobjects s2
cross join sysobjects s3</pre>

Now if we run the count we will see that the count for the table in the Test database has increased (as expected)

<pre>select COUNT(*) from TestSnapshot..TestTable
select COUNT(*) from Test..TestTable</pre>

<pre>Output
-------------
3025
169400</pre>

Okay now it is time to run sp_helpdb again

<pre>sp_helpdb 'Test'</pre>

<pre>Output
---------------------------------------------------------------
test		1	C:test.mdf		PRIMARY	20736 KB
test_log	2	C:test_log.LDF	        NULL	69760 KB</pre>

<pre>sp_helpdb 'TestSnapshot'</pre>

<pre>Output
---------------------------------------------------------------
test		1	C:test.mdf		PRIMARY	2304 KB
test_log	2	C:test_log.LDF	        NULL	1792 KB</pre>

Do you see that? When you run sp\_helpdb against the snapshot now it reports what we had originally for the Test database when we ran sp\_helpdb before adding rows to the table

Now run this query

<pre>select db_name(dbid) as DB_Name,name,filename,size * 8 from master..sysaltfiles
where dbid in (db_id('Test'),db_id('TestSnapshot'))</pre>

<pre>Output
---------------------------------------------------------------
DB_Name			name		filename	size
test			test		C:test.mdf	20736
test			test_log	C:test_log.LDF	69760
TestSnapshot	        test		C:testss.mdf	2304</pre>

As you can see, this reports the correct files and sizes for both Test and the snapshot.

So where is the problem? The problem is in the sysfiles table, it has the wrong filename

Run these two queries to verify that

<pre>select size * 8 as Size,filename from Test..sysfiles
select size * 8 as Size,filename from TestSnapshot..sysfiles</pre>

<pre>Output
----------------------------
Size        filename
20736       C:test.mdf
69760       C:test_log.LDF

Size        filename
2304        C:test.mdf
1792        C:test_log.LDF</pre>

sp\_helpdb actually calls the sys.sp\_helpfile proc to get the size and file name
  
run this and you will see that it is the second result set of sp_helpdb

<pre>exec Test.sys.sp_helpfile
exec TestSnapshot.sys.sp_helpfile</pre>

Inside the sys.sp_helpfile proc you will find the following code

<pre>select  name,  fileid, filename,  
 filegroup = filegroup_name(groupid),  
 'size' = convert(nvarchar(15), convert (bigint, size) * 8) + N' KB',  
 'maxsize' = (case maxsize when -1 then N'Unlimited'  
   else  
   convert(nvarchar(15), convert (bigint, maxsize) * 8) + N' KB' end),  
 'growth' = (case status & 0x100000 when 0x100000 then  
  convert(nvarchar(15), growth) + N'%'  
  else  
  convert(nvarchar(15), convert (bigint, growth) * 8) + N' KB' end),  
 'usage' = (case status & 0x40 when 0x40 then 'log only' else 'data only' end)  
 from sysfiles  
 </pre>

As you can see it uses the sysfiles table

If you want to see what the code for sp\_helpdb and sys.sp\_helpfile looks like execute the following

<pre>exec sp_helptext 'sp_helpdb'
exec sp_helptext 'sys.sp_helpfile'</pre>

So what do you think, is this a bug?

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][2] forum**<ins></ins>

 [1]: http://forum.lessthandot.com/viewforum.php?f=17
 [2]: http://forum.lessthandot.com/viewforum.php?f=22