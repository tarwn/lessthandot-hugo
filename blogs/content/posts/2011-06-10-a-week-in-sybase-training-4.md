---
title: A week in Sybase Training, what did I learn, day 5
author: SQLDenis
type: post
date: 2011-06-10T14:39:00+00:00
ID: 1214
excerpt: |
  This is day 4 of my training and today it is going to focus on Backups, Advanced Backup Techniques, Sybase Central and Monitoring the System
  
  Backups
  Instead of backup and restore Sybase uses the commands dump and load, SQL Server people will be fami&hellip;
url: /index.php/datamgmt/datadesign/a-week-in-sybase-training-4/
views:
  - 6964
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
This is day 5 of my training and today it is going to focus on Backups, Advanced Backup Techniques, Sybase Central and Monitoring the System

## Backups

Instead of backup and restore Sybase uses the commands dump and load, SQL Server people will be familiar with dump devices and the sp_addumpdevice stored procedure. In order to be able to perform backup, you first need to make sure that the Backup Server is running. You also need to add a dump, device, below is some sample syntax

sql
sp_addumpdevice "disk", "db_dump_device",
"/home/usr/u/someserver/sa/db_dump_device.dat"
```

**Database backups**
  
This is pretty much the same as SQL Server, it is a full database backup. Sybase does not have differential backups, Sybase has incremental backups and this is just a log backup. Sybase also has compression, you can go from level 0 to level 9, the higher the level, the smaller the file will be but the longer it will take and it will also use more CPU in that case.

Here is a sample dump statement

sql
dump database pubs2
    to "/dev/db_dump_device"
    with init
```

**Log backups**
  
Log backups are called incremental backups in Sybase, the command is dump tran, below are the 4 different variations

**dump tran**
  
will backup and removes the inactive portion

**dump tran with truncate_only**
  
removes the inactive part of the log without making a backup copy

**dump tran with no log**
  
removes the inactive part of the log without making a backup copy and without recording the procedure in the transaction log. Use no\_log only when you are completely out of log space and cannot run the usual dump transaction command. Use no\_log as a last resort and use it only once after dump transaction with truncate_only fails

**dump tran with no_truncate**
  
dumps a transaction log, even if the disk containing the data segments for a database is inaccessible, using a pointer to the transaction log in the master database. The with no_truncate option provides up-to-the-minute log recovery when the transaction log resides on an undamaged device, and the master database and user databases reside on different physical devices.

If you use dump tran with no\_truncate you must follow it with dump database, not with another dump tran. If you load a dump generated using the no\_truncate option, Adaptive Server prevents you from loading any subsequent dump.

Here is the complete syntax

<pre>dump tran[saction] database_name  
	to [compress::[compression_level::]]stripe_device
		[at backup_server_name]
		[density = density_value, 
		blocksize = number_bytes,
		capacity = number_kilobytes, 
		dumpvolume = volume_name,
		file = file_name]
	[stripe on [compress::[compression_level::]]stripe_device
		[at backup_server_name]
		[density = density_value, 
		blocksize = number_bytes,
		capacity = number_kilobytes, 
		dumpvolume = volume_name,
		file = file_name]]
	[[stripe on [compress::[compression_level::]]stripe_device 
		[at backup_server_name]
		[density = density_value, 
		blocksize = number_bytes,
		capacity = number_kilobytes, 
		dumpvolume = volume_name,
		file = file_name] ]...]
	[with { 
		density = density_value, 
		blocksize = number_bytes,
		capacity = number_kilobytes, 
		compression = compress_level,
		dumpvolume = volume_name,
		file = file_name,
		[dismount | nodismount],
		[nounload | unload],
		retaindays = number_days,
		[noinit | init],
		notify = {client | operator_console}, 
		standby_access }]
</pre>

**Restores**
  
Restores are named load in Sybase. Once you loaded a database it is in the offline state, you have to make it online
  
The load syntax is the same as the dump syntax. Instead of _dump_ you use _load_ and instead of _to_ you use _from_

Example

sql
load database pubs2 
    from "/dev/db_dump_device"
```

## Advanced Backup Techniques

This module went into some things you have to do if you want to do a point in time restore or if you lost the latest backup what you can do to backup to the most recent log backup.

## Sybase Central 

The other day I had some complaints about Sybase Central, I still think it sucks compared to SQL Server Management Studio but some of the things I complained about have been addresses
  
When I said that I didn't see results when running two commands, this is because by default Sybase Central only shows one result set. The icons are hideous, some of them have to be from NT4 or even windows 3.11
  
You will like this one…there is no shortcut or toolbar options to uncomment or comment a block of text….you have to go line by line or place /\* before and \*/ after the text you want to comment. You can't use multiple windows open in the same session, CTRL + N and it closes what you had open and you have a blank window, you have to open another instance of Interactive SQL to have multiple connections and sessions
  
For the rest, you will think that you are back using Query Analyzer

## Monitoring the System

This started with the error log, where errors are stored and severity levels of errors, also discussed was how to prune the error log.
  
Next up was sp\_who (what no sp\_who2? Nope, doesn't exist) and how to use KILL
  
Discussed next was how to monitor lock contention by using the sp\_oject\_stats stored procedure.

**MDA Tables**
  
These tables are similar to the sys.dm…… views in SQL Server. There are over 30 of these tables

**sp_sysmon**
  
This is a monitoring stored procedure, sp\_sysmon displays information about Adaptive Server performance. It sets internal counters to 0, then waits for the specified interval while activity on the server causes the counters to be incremented. When the interval ends, sp\_sysmon prints information from the values in the counters. See the Performance and Tuning Guide for more information.

<pre>Report section			Parameter
----------------------------	-------------
Application Management		appmgmt
Data Cache Management		dcache
Disk I/O Management		diskio
ESP Management			esp
Index Management		indexmgmt
Kernel Utilization		kernel
Lock Management			locks
Memory Management		memory
Metadata Cache Management	mdcache
Monitor Access to Executing SQL	monaccess
Network I/O Management		netio
Parallel Query Management	parallel
Procedure Cache Management	pcache
Recovery Management		recovery
Task Management			taskmgmt
Transaction Management		xactmgmt
Transaction Profile		xactsum
Worker Process Management	wpm</pre>

Here is a memory sample

sql
sp_sysmon begin_sample
go
-- do something or wait
sp_sysmon end_sample, memory
go
```
