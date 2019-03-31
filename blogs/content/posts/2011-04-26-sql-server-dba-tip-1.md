---
title: SQL Server DBA Tip 1 – Server Configuration – MAX Memory
author: Ted Krueger (onpnt)
type: post
date: 2011-04-26T15:58:00+00:00
excerpt: |
  For the first post in the SQL Server DBA Tip of the day series, MAX Memory configuration is the topic.  Let’s get right to it…
  SQL Server Maximum Memory configuration is a value specified to limit how much memory the buffer pool can have.  The Buffer P&hellip;
url: /index.php/datamgmt/dbadmin/sql-server-dba-tip-1/
views:
  - 19600
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
For the first post in the SQL Server DBA Tip of the day series, MAX Memory configuration is the topic.  Let’s get right to it…

SQL Server Maximum Memory configuration is a value specified to limit how much memory the buffer pool can have.  The Buffer Pool consists of dirty and clean pages that SQL Server has used.  The entire contents of the pool can be seen with the DMV sys.dm\_os\_buffer\_descriptors and you can see the state of a page using the is\_modified field.  This can be seen in great detail in a post by Paul Randal, “[Inside the Storage Engine: What’s in the buffer pool?][1]”  The page count per database in Paul’s post is extremely useful for an overview of the area that the pool is being used for most in terms of databases. 

When SQL Server starts, it uses the maximum (and minimum) memory settings to determine how much memory it can utilize for the buffer pool.  By default, the maximum memory is set to 2TB or 2147483647 KB.  The entire 2TB of memory is not used (or allocated) initially when SQL Server starts, but SQL Server will start taking the allocated memory up to the configured maximum memory value.  This is what some in the past have referred to as SQL Server’s memory leak.  It is far from a leak and completely by design.  Most small to mid-size installations are not going to have 2TB of memory installed on their database servers.  That is why this configuration is so critical to understand.  If the defaults are not reconfigured for the hardware and software that is currently installed, SQL Server will consume all of the memory it is allowed.  This has potential for causing a server to become unresponsive once under extreme pressure and at the high range of the allocated amount of memory. 

**Configuring Maximum Memory**

There are some loose standards out there on which you can base initial maximum memory settings.  Jonathan Kehayias is widely respected for his knowledge of SQL Server Internals and these settings.  In one of [Jonathan’s posts][2] for an installation checklist, he provides the guidelines of:

  1. 8GB RAM = 6144 Max Server Memory
  2. 16GB RAM = 12228 Max Server Memory
  3. 32GB RAM = 28672 Max Server Memory

That excerpt is slightly out of context when looking at 64bit to 32bit servers and available memory but sound.  The last point that Jonathan states is the most important:

  1. These are base values that will later be adjusted based on the MemoryAvailable MBytes counter being > 150 on the Server.

The monitoring of available memory is the most important factor in using a guideline.  Once you have promoted SQL Server to production or found an existing SQL Server that is not being monitored, Performance Monitor should be setup to start setting the baseline of your memory usage.  This will also show problematic SQL Servers that require an adjustment based on a limited amount of memory resources.  If low counts from MemoryAvailable Mbytes are found, investigate further by looking at the Buffer Manager: Buffer Cache hit ratio, page life expectancy and process: working set.   Those counters can base the needs for simple configuration changes or the need to increase memory in the server itself (physically). 

Resources:

  * [Server Memory Options][3]
  * [Memory Architecture][4]

 

 [1]: http://www.sqlskills.com/blogs/paul/post/Inside-the-Storage-Engine-Whats-in-the-buffer-pool.aspx
 [2]: http://sqlblog.com/blogs/jonathan_kehayias/archive/2010/03/22/sql-server-installation-checklist.aspx
 [3]: http://msdn.microsoft.com/en-us/library/ms178067.aspx
 [4]: http://msdn.microsoft.com/en-us/library/ms187499.aspx