---
title: Blocking due to fragmented HEAP Tables
author: Ted Krueger (onpnt)
type: post
date: 2009-04-24T10:30:09+00:00
ID: 396
url: /index.php/datamgmt/dbadmin/blocking-due-to-fragmented-heap-tables/
views:
  - 16210
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server Admin

---
Late last night my blackberry went nuts again. Sometimes I like that and sometimes I just want to keep sleeping. I know it may be a little odd to say I like having my database servers page me in the middle of the night, but troubleshooting problems is a major reason I went into the database administration field. Turns out the pages were all about blocking issues. Once I went into the blocks and drilled to batches that were abusing my database server, I found the reason to be a matter of fragmentation on a HEAP table. To date I still don't undertand HEAP tables. Well, I understand them. My point is, why use them? Is it really that hard to design tables so this is prevented? No, it's not. The problem still exists though and I had to fix it and fix it quick. Here is how I did. 

So I have a HEAP table with about 1 million rows in it. The HEAP table is fragmented to around 89% when I checked my fragementation logs and it bothers me to greatly. This bothers me mostly due to the fact this table is read countless times by the ERP system. As much as I want to call the designers of the ERP system up and school them on database design and concepts, we all know how far it will get me. So how I can I fix fragmented tables? Here is how…

Table name is POP10500. The ERP system is Microsoft Dyanmics Great Plains v9 so any alterations to the table structure will essentially take down the system. That means this is offline or after hours only. That pretty much goes for any tables you decide to do this to no matter how it affects the applications.

So I see my fragmentation by running the following

sql
SELECT  
	OBJECT_NAME(i.OBJECT_ID) AS TableName,
	i.name AS IndexName,
	indexstats.object_id
	,indexstats.index_id
	,index_type_desc
	,avg_fragmentation_in_percent
	,page_count
	,record_count
	,fill_factor
FROM    
sys.dm_db_index_physical_stats(DB_ID('DBA05'), NULL, NULL, NULL, 'DETAILED') indexstats
INNER JOIN sys.indexes i ON i.OBJECT_ID = indexstats.OBJECT_ID
AND i.index_id = indexstats.index_id
Where OBJECT_NAME(i.OBJECT_ID) = 'POP10500'
```
Which shows I'm at 89.41%. No one wants to see that number in the avg\_fragmentation\_in_percent column.

I know one key variable to my task of fixing this. The users and system tasks always hit column DEX\_ROW\_ID. I can create a nonclustered index on DEX\_ROW\_ID as

sql
CREATE NONCLUSTERED INDEX IX_DEX_ROW_ID
    ON dbo.POP10500(DEX_ROW_ID)
    WITH (FILLFACTOR = 70,
        PAD_INDEX = ON);
GO
```
But I also know that DEX\_ROW\_ID is unique. I can confirm that with

sql
SELECT COUNT(*),DEX_ROW_ID
FROM POP10500
GROUP BY DEX_ROW_ID
HAVING COUNT(*) > 1
```
And by determining the use of this table from the ERP documentation. Yes, you should read all the documentation not only for the databases you support, but for the applications you support. DEX\_ROW\_ID is simply therelational key to the header and detail purchase order tables. This table in question is a transactions tables. So why not kill the fragmentation all together taking advantage of DEX\_ROW\_ID? let's try…

sql
CREATE UNIQUE CLUSTERED INDEX IX_CLUS ON dbo.POP10500(DEX_ROW_ID);
GO
```
Let's check fragmentation now

sql
SELECT  
	OBJECT_NAME(i.OBJECT_ID) AS TableName,
	i.name AS IndexName,
	indexstats.object_id
	,indexstats.index_id
	,index_type_desc
	,avg_fragmentation_in_percent
	,page_count
	,record_count
	,fill_factor
FROM    
sys.dm_db_index_physical_stats(DB_ID('DBA05'), NULL, NULL, NULL, 'DETAILED') indexstats
INNER JOIN sys.indexes i ON i.OBJECT_ID = indexstats.OBJECT_ID
AND i.index_id = indexstats.index_id
Where OBJECT_NAME(i.OBJECT_ID) = 'POP10500'
```
0% 🙂 Happy days! Drop the clustered index

sql
DROP INDEX IX_CLUS ON dbo.POP10500
GO
```
Here is the thing. Dropping the index will create fragementation. Not much but it will. So check your fragmentation again. Mine goes to around 0.017%. This is much better than 89% of course.

Now let's say I didn't have DEX\_ROW\_ID available to use. I can add a unique column to the table
  
and cluster it. This will fix the fragmentation. You want to do your best to order the physical
  
data the way you want it though. Let's say PONUMBER is the column that is always filtered on.

sql
ALTER TABLE POP10500
	ADD CLUSCOL INT IDENTITY(1,1)
GO
CREATE UNIQUE CLUSTERED INDEX IX_CLUS ON dbo.POP10500(CLUSCOL,PONUMBER);
GO
DROP INDEX IX_CLUS ON dbo.POP10500
```
After all is said and done, I grabbed the query that the system was running and causing the blocks. Checked the plan and execution times prior to the de-frag steps. The query ran in around 11 seconds. Horrid!!! After physically ordering the table and removing the fragmentation I was getting sub second execution times. Blocking issues resolved!

This type of fix isn't the all saving grace for every HEAP table but in many situations it can really increase your performance. It's a great way to move HEAP tables to filegroups around on disk as well to increase performance for these huge and annoying tables 🙂