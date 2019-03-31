---
title: A week in Sybase Training, what did I learn, day 4
author: SQLDenis
type: post
date: 2011-06-09T17:54:00+00:00
excerpt: |
  This is day 4 of my training and today it is going to focus on Bulk Copy, Automatic Recovery, Checking and Fixing Database Consistency and Planning for Backups
  
  
  Bulk Copy
  
  Automatic Recovery
  
  
  Checking and Fixing Database Consistency 
  
  
  Plan&hellip;
url: /index.php/datamgmt/datadesign/a-week-in-sybase-training-3/
views:
  - 8076
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - ase
  - sql server
  - sybase
  - training

---
This is day 4 of my training and today it is going to focus on Bulk Copy, Automatic Recovery, Checking and Fixing Database Consistency



## Bulk Copy

**Getting the DDL**
  
The lesson started with showing how you can get the DDL for an object, you can use Sybase Central to do this or you can use the ddlgen Utility. Sybase Central is similar to SSMS, you right click on the object and select the Generate DDL option. The ddlgen utility is a Java based command line tool

Syntax looks like this

<pre>ddlgen 
	-Ulogin
	-Ppassword
	-S[server | host_name : port_number]
	[-I interfaces_file]
	[-Tobject_type]
	[-Nobject_name]
	[-Ddbname]
	[-Xextended_object_type]
	[-Ooutput_file]
	[-Eerror_file]
	[-Lprogress_log_file]
	[-Jclient_charset]
	-F[ % | SGM | GRP | USR | R | D | UDD | U | V | 
		P | XP | I | RI | KC | TR | PC ]</pre>

Here is what object_type (switch T) can be

<pre>Object type	Description
------------	------------------
C		cache
D		default
DB		database
DBD		database device
DPD		dump device
EC		execution class
EG		engine group
EK		encrypted keys
GRP		group
I		index
KC		key constraints
L		login
LK		logical key
P		stored procedure
R		rule
RI		referential integrity
RO		role
RS		remote server
SGM		segment
TR		trigger
U		table
UDD		user-defined datatype
USR		user
V		view
WS		user-defined Web service
WSC		Web service consumer
XP		extended stored procedure</pre>

Here are two examples. Both of these generate DDL for the primary and unique keys of all the tables in a database that begin with “PK”:

_ddlgen -Ulogin -Ppassword -TKC -Ndbname.%.%.PK%_

Or:

_ddlgen -Ulogin -Ppassword -TKC -N%.%.PK% -Ddbname_

**BCP**
  
This should look familiar if you have used bcp on SQL Server. Bcp has two speeds in Sybase.
  
Fast bcp, better performance but no recovery, used when a table doesn&#8217;t have indexes or triggers (or triggers have been disabled), you also have to have the minimally-logged operations enabled on the database
  
Slow bcp, slower performance but has better recovery, used when a table has at least one indexes or trigger
  
You can use bcp to copy data into partitioned tables by executing in parallel with different files in different sessions

You need these options turned on

<pre>exec sp_dboption MyDb, "select into/bulkcopy/pllsort", true
exec sp_dboption MyDb, "trunc log on chkpt", true</pre>

The bcp syntax is more or less the same as in SQL Server so I won&#8217;t go into any detail here.
  
One thing that bcp does not have in Sybase is the _queryout_, you can however create a view and do it that way

## Automatic Recovery

This was mostly about the log, dirty pages, when checkpoints occur, talking about the &#8220;trunc log on chkpt&#8221; option. Discussed was what happens when recovery kicks in, what happens to committed and uncommitted transaction, how to make recovery less long. Sybase doesn&#8217;t have the concept of fast recovery, the database is not available until all the transactions have been rolled forward or rolled back. There is a way to specify which databases you want recovered first after the system databases have been recovered, you do this with the sp\_dbrecovery\_order proc

**sp\_dbrecovery\_order**

Specifies the order in which user databases are recovered and lists the user-defined recovery order of a database or all databases.

<pre>sp_dbrecovery_order [database_name [, rec_order 
	[, force [ relax | strict ]]]] </pre>

Also discussed were full and incremental backups.

## Checking and Fixing Database Consistency 

Sybase just like SQL Server has the dbcc (database consistency checker) command
  
Here are some of them and what they call under the hood

dbcc checkdb, this calls the following dbcc command for every table
  
&#8230;dbcc checktable

dbcc checkalloc, this one will call these 3
  
&#8230;.dbcc tablealloc
  
&#8230;.dbcc indexalloc
  
&#8230;.dbcc textalloc

dbcc checkcatalog, this one checks the catalogs

dbcc checkstorage
  
dbcc checkstorage stores the output in a database named dbccdb, you have to create the dbccdb database yourself and then run the installdbccdb script that will create the objects you need. After dbccdb is created you need to configure parallelism, configure data cache + large I/O(sp\_cacheconfig and sp\_poolconfig), you need to create workspaces, you need to run the stored procedure sp\_dbbc\_updateconfig to set some attributes and finally you need to run sp\_dbcc\_evaluatedb to make sure it is all configured correctly.

Here is a list of most of them

tablealloc
  
checks allocation information for the specified table.

textalloc
  
checks allocation information in text pages for the specified table.

indexalloc
  
checks allocation information for the specified index.

checkalloc
  
runs the same checks as tablealloc, for all pages in a database.

checkcatalog
  
checks for consistency in and between system tables. For example, checkcatalog makes sure that every type in syscolumns has a matching entry in systypes, that every table and view in sysobjects has at least one column in syscolumns, and that the last checkpoint in syslogs is valid. checkcatalog also reports on any segments that have been defined. If no database name is given, checkcatalog checks the current database.

checktable
  
checks the integrity of data and index pages in the specified table.

checkdb
  
runs the same checks as checktable, for all tables in a database.

checkstorage
  
combines some of the checks of the above commands, and provides additional checks.

reindex
  
checks the integrity of indexes on user tables. prints a message when it finds the first index error and then drops/recreates the index.

Just so you know Sybase also has undocumented dbcc command, just like SQL Server
  
Here is a list of some of those
  
dbcc page
  
dbcc pglinkage
  
dbcc log
  
dbcc traceflags
  
dbcc traceon
  
dbcc traceoff
  
dbcc memusage

You can give dbcc permissions to a user (but why would you..as an admin, you should be running this)

_grant dbcc to user_
  
and
  
_revoke dbcc from user_

Finally if a database is completely FUBARed then you can use _dbcc dbrepair_ to drop it, you can drop a suspect database with a regular _DROP database_ command