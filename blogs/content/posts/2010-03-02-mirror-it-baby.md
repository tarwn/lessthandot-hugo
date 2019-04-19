---
title: Choosing operating modes for mirroring over a WAN
author: Ted Krueger (onpnt)
type: post
date: 2010-03-02T14:37:27+00:00
ID: 716
excerpt: Over the weekend on twitter, the topic of high availability over a Wide Area Network (WAN) came up. The limit of 140 characters doesn't do this topic justice, so a follow up is a good idea.....
url: /index.php/datamgmt/dbprogramming/mirror-it-baby/
views:
  - 11348
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
## To sync or async...

Over the weekend on twitter, the topic of high availability over a Wide Area Network (WAN) came up. The limit of 140 characters doesn't do this topic justice, so a follow up is a good idea.  <img src="/wp-content/uploads/blogs/DataMgmt/breakmirror.gif" alt="" title="" width="325" height="325" align="right" />We're going to focus on selecting an operating mode of mirroring that the business can handle then the mirror is spread over a WAN. This also came out of the conversations when synchronous mirroring (High Availability) was mentioned in a way of being over Asynchronous (High performance). To answer the question directly, High performance cannot provide you with out of the box automated failover and there is the chance for data loss. So for an absolute option of High Availability, High performance cannot fully replace it. There are options available when the performance load of synchronous mirroring with a witness is far too much for the business to carry. 

There are a few considerations to take into account when mirroring over a WAN. The primary consideration is the latency that a wide area network brings along with it. From personal testing, results for mirror configurations in this situation, have shown an estimated performance hit of ~roughly 90% on basic transactions like INSERT, UPDATE and DELETE. The tests that can be performed to gauge operating modes do not have to go indepth. That is not to say that testing a full baseline across the normal operating hours of the business should not be done. In fact, it is critical and should never be bypassed because simple statements pass your test cases. 

## Crude but peace of mind results...

To show this, we're going to take a database located in the far north region of the US and a mirror located in the far south region (roughly 600 Miles apart). In order to show both operating modes we are using Enterprise in this test case. True asynchronous mirroring is only available in Enterprise edition. This is a key aspect to researching the edition and purchasing the right SQL Server edition for your company. 

To create the database to test, perform the following steps
  

  
On the principal (north region) create the primary database
  


```sql
CREATE DATABASE [remotemirror_deleteon03032010] ON  PRIMARY 
( NAME = N'remotemirror', FILENAME = N'C:remotemirror.mdf' , SIZE = 3072KB , MAXSIZE = UNLIMITED, 
FILEGROWTH = 100MB )
 LOG ON 
( NAME = N'remotemirror_log', FILENAME = N'C:remotemirror_log.ldf' , SIZE = 1024KB , MAXSIZE = 1024GB , 
FILEGROWTH = 10MB)
GO
```

Next, take our initial full backup followed by a tail-end transaction log backup
  


```sql
BACKUP DATABASE [remotemirror_deleteon03032010] TO  DISK = N'C:delelet_remotemirror.bak' 
WITH NOFORMAT, NOINIT,  NAME = N'remotemirror-Full Database Backup', SKIP, NOREWIND, NOUNLOAD,  STATS = 10
GO

BACKUP LOG [remotemirror_deleteon03032010] TO  DISK = N'C:delelet_remotemirror.bak' 
WITH NOFORMAT, NOINIT,  NAME = N'remotemirror-Transaction Log  Backup', SKIP, NOREWIND, NOUNLOAD,  STATS = 10
GO
```

On the designated mirror (southern region) restore the full and tail end transaction log
  


```sql
RESTORE DATABASE remotemirror_deleteon03032010 FROM  DISK = N'D:delelet_remotemirror.bak' 
WITH  FILE = 1,  NORECOVERY,  NOUNLOAD,  REPLACE,  STATS = 10
GO

RESTORE LOG remotemirror_deleteon03032010 FROM  DISK = N'D:delelet_remotemirror.bak' 
WITH  FILE = 2,  NORECOVERY,  NOUNLOAD,  STATS = 10
GO
```

Refer to this blog, “[Using Mirroring to Reduce DB Migration Downtime (Part 1)”.][1] 

The tests will be based around simple CREATE TABLE, INSERT, UPDATE and DELETE statements. The resulting client statistics from the transactions will be used to show the total differences in execution time. 

To set client statistics on from SSMS, click the, “Include Client Statistics” icon in the menu strip or to get the full execution time which is used primarily in this test, add SET STATISTICS TIME ON to the beginning of the query

Again, no complexity is added so the statistics.
  

  
Execute the following statements step by step. 

```sql
CREATE TABLE MIRROR_TST_01 
(
ID INT IDENTITY(1,1)
,COL1 VARCHAR(10)
,COL2 VARCHAR(15)
,COL3 VARCHAR(35)
)
GO
INSERT INTO MIRROR_TST_01
VALUES
('Test','Test Insert #1','############################'),
('Test','Test Insert #2','############################'),
('Test','Test Insert #3','############################'),
('Test','Test Insert #4','############################'),
('Test','Test Insert #5','############################'),
('Test','Test Insert #6','############################'),
('Test','Test Insert #7','############################'),
('Test','Test Insert #8','############################'),
('Test','Test Insert #9','############################'),
('Test','Test Insert #10','############################'),
('Test','Test Insert #11','############################'),
('Test','Test Insert #12','############################'),
('Test','Test Insert #13','############################')
GO

UPDATE MIRROR_TST_01 SET COL1 = 'Update Row' WHERE ID = 5
GO
DELETE FROM MIRROR_TST_01 WHERE ID = 10
GO
DROP TABLE MIRROR_TST_01
```

GO 

Gathering the client statistics information, the test results from the above in all operating modes is shown as:
  


<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/crudestats.gif" alt="" title="" width="448" height="397" />
</div>

The execution time comparisons are:
  


<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/mirrir.gif" alt="" title="" width="628" height="177" />
</div></p> 

It is surprising and interesting that High Protection results have a higher average than High Availability, however, as previously mentioned, the tests are crude. They were executed 20 times to obtain the average. Keep in mind that network traffic is all part of this equation and a very important piece. Although this was on a dedicated data line, there is still log shipping and other data packets contending with bandwidth. 

## No surprises...

There is no hidden agenda in the operating modes for mirroring. There are no huge catches between performance, protection and availability. The operating modes truly are what they say they are. We can see as with any asynchronous operations, performance is much better overall as compared to the synchronous operating High Protection and Availability modes. For working with mirroring over a WAN, there are still alternatives to an automatic failover. Those alternatives bring the need for a DBA to write his or her own monitoring tools and react to events in mirroring but they can be very effective and allow you to have high performance while still staying in a high(er) availability mode. 

Paul's blog on [monitoring mirroring][2] is excellent and can be used to monitor and force some condition events to failover mirrors that are in high performance operating mode. This can act as a witness type monitoring device. Be careful how you determine to failover when it comes to a WAN. Things like the witness work off ping to the principal and mirror. By default, this there is a 10 second threshold, so the concept of a WAN may force the 10 second threshold to vary. Typically, this is set higher to prevent unwanted failovers to your mirror in a WAN landscape. 

## Research for the advantage...

Once again, these statistics are brief and a rough drawing as mirroring goes. The following documentation should be reviewed from BOL while in the planning phases of implementing mirroring, “[Database Mirroring Concepts][3]” 

Performance characteristics have been set in another broad range to fill some variables that are required to pick the correct operating modes.

 [1]: /index.php/DataMgmt/DBAdmin/move-databases-to-new-server-with-little-1 on configuring and starting mirroring
 [2]: /index.php/DataMgmt/DBAdmin/how-to-monitor-database-mirroring
 [3]: http://msdn.microsoft.com/en-us/library/cc917681.aspx#ELAA