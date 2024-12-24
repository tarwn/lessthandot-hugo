---
title: Mirroring SQL Server 2005 to SQL Server 2008 (Part 1)
author: Ted Krueger (onpnt)
type: post
date: 2010-01-11T12:51:05+00:00
ID: 668
url: /index.php/datamgmt/datadesign/mirroring-sql-server-2005-to-sql-server-2008/
views:
  - 28160
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/mirror.gif" alt="" title="" width="550" height="678" />
</div>

Over the weekend I started writing my documentation and lab work for a few database servers that I will be upgrading to SQL Server 2008 in the first quarter of 2010. Upgrading SQL Server has benefits in having multiple ways to go about how you plan and execute the process. I can't praise the SQL Server team enough for making the task of upgrading much less stressful than other database servers I have worked on.
  

  
The company I currently work for has little tolerance to downtime. Seconds can do severe damage to the process flow of money in and out. This means that my process of maintenance, upgrades, migrations and such must be quick with little disruption to the business. One method I've adopted is to use mirroring for migrations and moving to other hardware for SQL Server. This method has worked exteremely well and I wanted to test feasiblity for upgrading as well. Given the concept, this can be a very powerful method of upgrading to new versions of SQL Server now that mirroring is on the list of features for SQL Server Enterprise and Standard editions. 

## What I found in my tests 

SQL Server 2005 SP3 Enterprise had no problem mirroring to SQL Server 2008 Enterprise. The same mirroring principles apply however in this situation. You are limited to mirroring the same editions. That means you cannot take SQL Server 2005 SP3 standard and attempt to mirror it to SQL Server 2008 Enterprise. So far I have completely tested all modes of mirroring with great success. These tests included data and objects manipulation and creation. All passed and the mirror retained integrity from the principle. I also tested asynchronous mirroring along with synchrounous mirroring. All of my tests did include a witeness so safety levels were on. 

## Some cons to watch for

  * You are restricted on the mirror for snapshots. Snapshots are a good way to take advantage of a mirror for read-only abilities. Since the restore of the 2005 database will need to stay in recovering, the database version is set to 611. SQL Server 2008 is version 655 and thus will not allow you to create snapshots on the database until it is upgraded to 655.
  * This is of course a one way mirroring path. Given the known fact you cannot restore a SQL Server 2008 database to SQL Server 2005, there is no method of going back.
  * Next problem is, if you bring the database on the 2008 side out of recovering for any reason, it will be upgraded to version 655 without the ability to stop the upgrade. Meaning if you do, 
    sql
restore database junk with recovery
```

    your database will output the following
    
    
```

Converting database 'JUNK' from version 611 to the current version 655.
    Database 'JUNK' running the upgrade step from version 611 to version 621.
    Database 'JUNK' running the upgrade step from version 621 to version 622.
    Database 'JUNK' running the upgrade step from version 622 to version 625.
    .........
    Database 'JUNK' running the upgrade step from version 652 to version 653.
    Database 'JUNK' running the upgrade step from version 653 to version 654.
    Database 'JUNK' running the upgrade step from version 654 to version 655.
    RESTORE DATABASE successfully processed 0 pages in 1.825 seconds (0.000 MB/sec).
```

  * Once you failover to the new mirror, there is no mirroring back to 2008. This means your failover is final. The only way to get back is to set mirroring back up from 2005 to 2008.

## Failing back?

Once you initiate your failover, you can back the SQL Server 2008 database up while still in 90 compatibility and restore back to SQL Server 2005. This is your only fail-back method however given the fact you cannot restore back in recovering and attempt to initialize mirroring from SQL Server 2008 to 2005.

One theory that I am still testing is getting the upgraded version of 655 databases from SQL Server 2008 back to 2005. Then setup mirroring again to eliminate the snapshot problems.

Once my labs are done I will post back in "part 2" with a complete process flow and steps to take for this type of upgrade path. For now and if you question mirroring from the same editions of SQL Server 2005 to SQL Server 2008, the answer is yes. You very well can do this. If you have questions on using mirroring for migrations of SQL Server, you can read a detailed step-by-step process from the following links.
  
[Using Mirroring to Reduce DB Migration Downtime (Part 1)][1]
  
[Using Mirroring to Reduce DB Migration Downtime (Part 2)][2]

Remember, _never_ upgrade SQL Server without testing your upgrade path and methods you utilize. Even knowing I say it works doesn't me you can skip that critical stage in upgrading SQL Server.

To read part 2 and see this in action, please read on with [Mirroring SQL Server 2005 to SQL Server 2008 (part 2)][3]

 [1]: /index.php/DataMgmt/DBAdmin/move-databases-to-new-server-with-little-1
 [2]: /index.php/DataMgmt/DBAdmin/using-mirroring-to-reduce-db-migration-d-2
 [3]: /index.php/DataMgmt/DBAdmin/mirroring-sql-server-2005-to-sql-server--2