---
title: Red Gate's new SQL Index Manager
author: Ted Krueger (onpnt)
type: post
date: 2011-12-08T16:36:00+00:00
ID: 1425
excerpt: 'Today, Red Gate announced a new product in beta testing, SQL Index Manager.  The new tool will be used to analyze and recommend solutions to index fragmentation on a SQL Server Instance.  The first thing you may ask yourself is: another tool to fix frag&hellip;'
url: /index.php/datamgmt/dbadmin/red-gate-sql-index-manager/
views:
  - 10319
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
Today, Red Gate announced a new product in beta testing, [SQL Index Manager][1].  The new tool will be used to analyze and recommend solutions to index fragmentation on a SQL Server Instance.  The first thing you may ask yourself is: another tool to fix fragmentation?  There are quite a few index scripts and tools out there to handle fragmentation.  Take a look at [Michelle Ufford's index maintenance script][2].  I've used Michelle's script for quite some time now and it is really stable and does a great job.

The one thing we will get by another index maintenance tool that comes from Red Gate is, quality and the name.  Red Gate is a well-respected company and known for providing us, SQL Server Professionals, with solid tools to help us get the job done efficiently.  I mean, who hates the [Toolbelt][3] bundles?  I've never heard many people say they hate it or haven't used a Red Gate SQL Server product.

**First look at Index Manager**

Note: The SQL Index Manager is in **Beta** so anything I report here is just as much a suggestion to the product team as it is my first review.  This is not an RTM product yet.

I do have to say, I didn't like the first thing I saw about this tool.  You are presented with a Connect to server dialog and a Diagnose now...button.  Sorry but that would be a, No.  I'm not going to put a production instance or even highly active development instance in there and hit diagnose without being able to limit what is diagnosed.  So that was a big thing that wasn't really great.

There also seems to be a little bug on the initial loading of the tool in which it does not prepopulate the instances it finds.  Hit cancel and then FileàConnect to a server, the list populates then.

Entering in a SQL Server 2012 RC0 instance, I quickly received a message stating, "No fragmented Indexes were found".  Took me a minute to find the "Reanalyze" button so if you are looking up, look down. It is on the bottom menu strip, in the left corner.

**Internals**

What is this new tool doing in the background?  This is what I can see the tool is doing to my instances.

One thing I noticed is, it does not seem to take into account if the edition of SQL Server is Enterprise.  So one has to ask the question if the tool is accounting for online operations with Enterprise Edition?

I was happy to see the first thing Red Gate does is weed out system databases, but didn't include databases like distribution or such that are used by replication.  Those system databases are often and commonly indexed and require analysis.  The tool is also filtering out databases that are in standby, offline and snapshots.  I think the query that is used could use some [ ] around name though (ok, now I'm being really picky)

```sql
SELECT name AS databaseName
     , database_id AS databaseId
FROM master.sys.databases
WHERE name NOT IN ('master', 'msdb', 'tempdb', 'model')   
  AND is_in_standby <> 1                                 
  AND state_desc = 'ONLINE'                              
  AND source_database_id IS NULL
```

 

Once the list of databases has been grabbed, the tool moves onto retrieving a listing of all the tables and views by querying sys.obects and sys.schemas.  I won't post those queries but they appear to be well formed, as are most of the queries that sit behind these types of Red Gate products.  I immediately looked for the handling of LOB and the tool is taking care of it.

The next steps are to go into checking for all the indexes by querying sys.indexes and sys.partitions.  Of course, the first predicate is to ignore my sad HEAP tables that are cluster orphaned.

It appears that the SQL Index Manager retrieves all the object and index metadata before doing any true analysis of fragmentation.  The first thought I have here is the cost in memory consumed locally by the machine running the SQL Index Manager.  Having one SQL Server database with enough returned resulted in memory is one thing but I could easily see an entire instance being able to return and fill a nasty chunk of memory with this method.  It reminds me of the documenting tool and it erroring out if you try to script too many objects in one database.

On to the good stuff: SQL Index Manager is more than likely dynamic T-SQLing out the findings in both results from the objects set and the index set with some sort of merge there.  The tool proceeds to reply on the DMV sys.dm\_db\_index\_physical\_stats to retrieve the avg\_fragmentation\_in\_percent and page\_count.  Yes, page count does make a difference when asking, "Do we need to defrag something with 2 pages?"

An example of that call

```sql
exec sp_executesql N'
SELECT index_id
     , avg_fragmentation_in_percent
     , page_count
FROM sys.dm_db_index_physical_stats(@databaseId, @objectId, @indexId, @partitionNr, NULL)
',N'@databaseId int,@objectId int,@indexId int,@partitionNr int',@databaseId=6,@objectId=341576255,@indexId=1,@partitionNr=1
```
 

This is all evaluated and then either the message I received is returned or the tool moves onto some more meaty decisions.

I analyzed a much more utilized SQL Server instance next to go into how the tool results and lets me decide what to do.  The local databases consisted of 1240 indexes of varying sizes.  The total analysis took an estimated 5 minutes.  Now that in no way can be used as a gauge against how well the tool performs.  This is a first look.  I'll do deep performance tests of the tool after beta.

**Analysis**

The Index Analysis report grid that is shown is extremely easy to read and quickly identifies highly fragmented indexes.  One thing I would like to see in the RTM is the ability to sort by Database.  Clicking the header does not sort the results this way.  This is another request to allow me, the DBA, to have the ability to restrict this to only the databases I want to analyze.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-76.png?mtime=1323369284"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-76.png?mtime=1323369284" width="624" height="424" /></a>
</div>

I need to check the help documentation but I didn't see how the page count was being calculated to provide either a low, medium or high level of fragmentation.  Since the page count and calculations look to be internal and without breaking laws to see them, I'm not sure what they are using for numbers.  I'm guessing it will be known though.  Again, this is a first look as I would give it without diving real deep into something.  First impressions are everything!

**Overall First Impression**

I'm hoping Red Gate adds the ability to restrict to only analyzing specific databases on an instance.  If they do that, I think it will add a good feature to the tool.  Overall, I think the new tool does a good job at making it easy to analyze and make a decision on what to defrag for indexes.  You have a simple grid that shows a bar of what is a highly fragmented index and simply check a box to fix it.  I just hope no one falls into an event where they analyze an entire instance and then leave everything checked and defragments them all during high activity times.  The other thing I was unable to determine is if the tool takes advantage of online operations if Enterprise is detected.

 [1]: http://www.red-gate.com/products/dba/sql-index-manager/
 [2]: http://sqlfool.com/category/performance-tuning/
 [3]: http://www.red-gate.com/products/sql-development/sql-toolbelt/