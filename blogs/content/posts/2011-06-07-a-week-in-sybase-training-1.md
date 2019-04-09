---
title: A week in Sybase Training, what did I learn, day 2
author: SQLDenis
type: post
date: 2011-06-07T18:13:00+00:00
ID: 1209
excerpt: |
  This is day 2 of my Sybase training.
  
  I forgot to mention yesterday, half of the people are in this class because the Sybase DBA quit and never say the O word (O like in Oracle)
url: /index.php/datamgmt/datadesign/a-week-in-sybase-training-1/
views:
  - 9097
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
This is day 2 of my Sybase training.

I forgot to mention [yesterday][1], half of the people are in this class because the Sybase DBA quit, another thing that was said yesterday was to never say the O word (O like in Oracle).

Today we continued with configurations. Sybase has something called a data cache, you can have more than 1 cache. to increase the default data cache to 100MB you would issue the following command

sql
sp_cacheconfig 'default data cache', '100M'
```

to create a new cache name SQLMenace with 10MB, your command would look like this

sql
sp_cacheconfig SQLMenace, '10M'
```

To drop the cache that is named SQLMenace your command would look like this

sql
sp_cacheconfig SQLMenace, '0'
```

All in all it seems you have to configure a lot more in Sybase compared to SQL Server. It looks like if you have configured to have 200 connection or 10000 open indexes that you have to monitor this, then you have to adjust this if you see that you have configured too much or if user complain that they can't connect you need to configure for more connections

#### Tools

I will never ever complain about SSMS again. Interactive SQL in Sybase Central brings all the pains (without the joys) of Query Analyzer back to the present day. I can't even find a shortcut to quickly comment or uncomment a selection. If you want to run a selection you have to press F9, F5 will run the first statement on a page only. But F9 is not any better it will only run the first line in a selection, if you put a GO in between it only runs the second command…..I can see the making of big mistakes if you are not aware of this behavior.

#### Devices

Devices in Sybase are similar to what files are in SQL Server. There is one interesting thing that happens upon install, remember I told you yesterday that Sybase places the master database on the master device, guess what, the master device is the default device. After installation you should make this device not default. You can make a device default or not by executing the following command

sp_diskdefault DeviceName, defaultoff|defaulton

so to make master not default, you would do

sql
sp_diskdefault master, defaultoff
```

You can have many devices just like you can have many files in SQL Server. <del>I have so far not seen a way that you can group these like we have filegroups in SQL Server</del> I was wrong I think, I guess these are segments

Here is how you create a new device

sql
disk init 
name = "my_device", 
physname = "/usr/u/sybase/data/my_device.dat", 
vdevno = 2, size = 5120, dsync = true
```

A couple of things…
  
**name** is the name
  
**vdevno** is the unique filenumber, you can create one between 1 and 2,147,483,647 or you can have the system assign one
  
**physname**(yes that was a typo) is the physical location of the file
  
**size** is the size
  
**dsync** specifies whether writes to the database device take place directly to the storage media, or are buffered when using operating system files. This option is meaningful only when you are initializing an operating system file; it has no effect when initializing devices on a raw partition. By default, all operating system files are initialized with dsync set to true.

On Unix/Linux you can have files on raw partitions or on the filesystem

When you drop a device by using sp_dropdevice, you also have to remove it from the filesystem, it does not do this for you.

**Databases**
  
Sybase needs a lot more hand holding compared to SQL Server when creating databases. It is very important to save the scripts that you used to create the databases. If on one server you have 2 data files and 1 log 1 and on the other server you create the same but the order is not the same…you have a lot of problems (also know as job security I guess, you need to document everything, you can also grab this info from the sysusages table and them do some math to get to the same sizes)

Sybase has something called segments….I guess it is similar to a filegroup.
  
Here is what the documentation says

> A segment is a label that points to one or more database devices. Segment names are used in create table and create index commands to place tables or indexes on specific database devices. Using segments can improve Adaptive Server performance and give the System Administrator or Database Owner increased control over the placement, size, and space usage of database objects.

By default in Sybase tables will be created with not null as default, you can override this with the _allow nulls by default_ option, you can use sp_dboption for this.
  
There is also an option to create an identity column on every table by default. You can create automatic IDENTITY columns by using the auto identity database option and the size of auto identity configuration parameter

Here is what it looks like

sql
sp_dboption database_name, "auto identity", "true" 
```

You need to then run a checkpoint so that the in-memory structure _dbtable_ gets refreshed

**Thresholds**
  
This is pretty cool, in Sybase you can use the proc sp_addthreshold to fire another proc when free space falls below a certain threshold

**sp_addthreshold**
  
Creates a threshold to monitor space on a database segment. When free space on the segment falls below the specified level, Adaptive Server executes the associated stored procedure.

Example

sql
sp_addthreshold mydb, segment1, 200, pr_threshold_warning
```

Creates a threshold for segment1. When the free space on segment1 drops below 200 pages, Adaptive Server executes the procedure pr\_threshold\_warning

You can also use sp_helpthreshold for information about existing thresholds.

Another use of thresholds is in a database to expand the database when you hit the threshold (similar to autogrow in SQL Server)

 [1]: /index.php/DataMgmt/DBAdmin/a-week-in-sybase-training