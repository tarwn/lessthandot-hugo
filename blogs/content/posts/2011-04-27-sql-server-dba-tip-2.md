---
title: SQL Server DBA Tip 2 – Server Configuration – Data/Log Files
author: Ted Krueger (onpnt)
type: post
date: 2011-04-27T10:19:00+00:00
ID: 1128
excerpt: |
  The second SQL Server DBA Tip is going to cover placing data files.  There are a few primary concerns when deciding where to place data and log files.  System Databases, I/O Overall Performance and Space Considerations will be the focus today.
  Storage&hellip;
url: /index.php/datamgmt/datadesign/sql-server-dba-tip-2/
views:
  - 18395
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
The second SQL Server DBA Tip is going to cover placing data files.  There are a few primary concerns when deciding where to place data and log files.  System Databases, I/O Overall Performance and Space Considerations will be the focus today.

Storage comes in many forms and configurations.  With Solid State Disk coming into the mix with Hard Disk Drives, a new avenue is paved in performance measurements.  SSD comes with an extremely high price tag and this has kept it from truly taking a hold in all data centers.  As demand goes up and manufacturing increases, price will undoubtedly come down.  Until then, making HDD work as optimally as possible is still a major factor.

**Design Planning – Placement is everything**

Before deciding on space required and configurations such as RAID levels, we have to determine the locations for the files that are required to support SQL Server.  In the perfect world, all files could be placed on any available disk and function to the best of their quality at all load levels.  For one, this is far from true, and determining the proper separation of data and log files is critical.  Second, the location of the data and log files needs to be a factor. 

_A general guideline to follow:_

<div class="image_block">
  <a href="/media/blogs/DataMgmt/-44.png?mtime=1303501294"><img src="/wp-content/uploads/blogs/DataMgmt/-44.png?mtime=1303501294" alt="" width="700" height="213" /></a>
</div>

**System Databases**

TempDB: The TempDB is a crucial system database in SQL Server.  There are countless transactions that rely on the space in TempDB to complete successfully.  Some of those tasks require <span style="text-decoration: line-through;">a</span> repeated requests back to that temporary storage and some destroy it immediately.  All of this requires TempDB to be treated differently than other databases.  All of the different transactions that are trying to use the same resources can become a massive bottleneck.  To handle this, the loose recommendation is a 1 to 1 configuration of TempDB data file to Processor Core.  I'm not an advocate of this as a best practice any longer.  On SQL Server 2000 this may have been more relevant, but it isn't as now.  With new chip technology and logical cores exceeding a number like 30, there should be more investigation involved in determining the ratio of TempDB files to Core. 

Paul Randal and his post titled, [TempDB should always have one file per processor core][1], goes over why this isn't a great idea to follow the older guidelines.  The tip from this post is to base your initial slicing of the TempDB with the number of TempDB files equaling ¼ of the number of cores.  This is a base starting point.  As Paul went over in his post; if latch issues arise with TempDB, then look deeper into re-configuring the file counts to help improve performance and lower the wait times.  Utilizing Glenn Berry's script through Paul's post is a great way to do this. 

The other System Databases <span style="text-decoration: line-through;"></span>like master, msdb or model should be placed on their own disk.  This is more for recovery than performance.  These databases do not grow a lot, but keep in mind that msdb holds data that involves many objects that can require space.  SQL Server Agent uses msdb, and with uncontrolled history on job executions, the tables can grow quickly. 

**I/O Performance**

Once the time has been taken to analyze all the needs that your subsystem requires, the next critical step is determining the type of disk that should be used.  Small to mid-sized companies commonly have limited resources and SAN technology is not available.  Disk arrays are more common in these situations and are a viable solution when space is needed.  With both, the RAID (Redundant Array of Independent Disks) level needs much attention.  In the illustration above, the images are shown as mirrored drives and RAID.  Beyond the Operating System binaries of the server, RAID 5 and RAID 10 (RAID 1+0) are the most common choices.  Although there are many other RAID levels that can be used, these two are viable and available for a reasonable cost.  Remember, SSD brings in a completely different performance factor and that is not a focus yet.  There has always been a sense in the DBA worlds that when at all possible, use RAID 10.  This includes a stripe of mirrors, which forces the needs for a minimum of 4 physical disks.  However, cost needs to be factored in.

When determining which RAID level to use, OLTP vs. OLAP is going to be the deciding factor:  Write vs. Read.  True OLTP SQL Server setups will be a primary write situation.  In a lot of cases, however, what is thought to be OLTP in reality has more read actions occurring than assumed.  When possible with existing installations, monitoring these write and read events with performance monitor before deciding which events are more predominant on the disks will assist in making the right decision on RAID levels. 

Kendal Van Dyke did a [deep dive look][2] into performance between the RAID levels in 2009.  This test case and results can still be used as a guideline. 

When RAID levels are chosen, testing the performance before actually using your disk is the next step.  [SQLIO][3] and SQLIOSim ([X64][4] and [X86][5]) are available to test the performance of the disks.  These tools allow heavy loads to be placed on the disk and return relatively accurate results on how well the disk performed during file activity.  With the results, disks can be re-aligned and reconfigured as needed.  It's a bad practice to assume your disks are configured correctly and will perform without testing them beforehand.  Performance problems with I/O are much more difficult to fix after databases are actively using them.

**Space Considerations**

Each file that is part of the SQL Server installation requires specific space considerations.  Although the term of, “Disk is Cheap” is true, a lot of teams still have difficulty requesting additional disk simply to have it available for future expansion.  There is a need for DBA teams to make a sound case for the expense.  Teams can do that by outlining the possible growth of each specific file.

Log file sizing <span style="text-decoration: line-through;">is</span> can be done, in an extreme generalization, by determining the largest table (or Clustered Index) that exists on the database and doubling it.  This prevents growth concerns with the logs while index maintenance is being performed.  Further considerations for normal import operations can also factor into sizing log files.  This <span style="text-decoration: line-through;">is</span> can prove to be useful in determining the amount of disk to request to support log files.

Data files have an initial size and then the actual space being utilized.  The use of a percentage of free space is to prevent performance-hindering growth events.  When determining the amount of disk required for data files, take into account the space used and then space free that would be ideal.  Factor in normal data growth and future expansion needs.  If the SQL Server installation is new, a generally sound request is to double the data size, with an open-ended size needed.  Once the installation is in production, monitor the daily growth to determine exactly what may be the potential growth over the next 3 years.  Base this potential growth on how much disk is required.

 

 [1]: http://www.sqlskills.com/BLOGS/PAUL/post/A-SQL-Server-DBA-myth-a-day-(1230)-tempdb-should-always-have-one-data-file-per-processor-core.aspx
 [2]: http://www.kendalvandyke.com/2009/02/disk-performance-hands-on-part-5-raid.html
 [3]: http://www.microsoft.com/downloads/en/details.aspx?familyid=9A8B005B-84E4-4F24-8D65-CB53442D9E19&displaylang=en
 [4]: http://download.microsoft.com/download/6/5/2/65286f65-bff2-42b8-b0c9-87f117855069/sqliosimX64.exe
 [5]: http://download.microsoft.com/download/3/8/0/3804cb1c-a911-4d12-8525-e5780197e0b5/SQLIOSimX86.exe