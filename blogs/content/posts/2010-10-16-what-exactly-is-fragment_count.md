---
title: What exactly is Fragment_count?
author: Ramireddy
type: post
date: 2010-10-16T05:24:26+00:00
excerpt: 'I started to think about this problem, when I saw this question asked by Sankar Reddy in SQL Server Quiz 2010. I have a fair bit of idea about Index fragmentation and defragmentation. I checked fragmentation of some of my table indexes previously and re&hellip;'
url: /index.php/datamgmt/datadesign/what-exactly-is-fragment_count/
views:
  - 11492
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Microsoft SQL Server

---
I started to think about this problem, when I saw [this][1] question asked by Sankar Reddy in SQL Server Quiz 2010. I have a fair bit of idea about Index fragmentation and defragmentation. I checked fragmentation of some of my table indexes previously and rebuilt the indexes when fragmentation percentage is too high. But I never thought about how exactly these will be calculated. But after looking at this question, I thought of finding how SQL server will calculate this. Let’s have a look at this example.

<pre>create table tblNumbers
(
	Id int identity(1,1) primary key,
	Num int
)

;with N as
(
	select 0 as Num union all select 0 union all select 0 union all select 0 union all select 0 union all
	select 0 union all select 0 union all select 0 union all select 0 union all select 0
),
Numbers as
(
	select ROW_NUMBER() over (Order by (select 1)) as rn from N N1,N N2,N N3,N N4,N N5, N N6
)
insert into tblNumbers
select rn from Numbers 

SELECT page_count, fragment_count
FROM sys.dm_db_index_physical_stats (DB_ID(), OBJECT_ID('tblnumbers'),NULL, NULL, 'detailed') 
where index_id = 1 and index_level = 0</pre>

In the above example we are inserting 1 Million records and checking the Physical stats of the Clustered index of the table. It shows 3832 pages were allocated to the clustered index in its Leaf Level and shows Fragment count as 17. 

A Fragment is a collection of pages in sequence. Assume there is a page with ID 1000 is allocated to a table, and its sequences are 1001 and 495 instead of 1002, these will be considered as 2 fragments with one fragment having 1000-1001 and other fragment with 495.

In one way, a Fragment can also be indicated as a part. In our example, the CI has 17 fragments and 3832 pages in Leaf Level. So, we can say, 3832 pages are occupied across 17 parts (Fragments). Each of these parts will have its pages in sequential order. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/Fragments.jpg" alt="" title="" width="900" height="300" />
</div>

Let’s try to implement this method. The idea is to first load all the pages allocated to the Clustered Index into a temporary table and assign the sequence number to those pages and join each record with next record and find out the records where the difference is not equal to 1.

<pre>-- Table to hold Pages of the CI,
CREATE TABLE sp_tablepages
(
ID int identity(1,1) primary key,
PageFID tinyint,
PagePID int,
IAMFID tinyint,
IAMPID int,
ObjectID int,
IndexID tinyint,
PartitionNumber tinyint,
PartitionID bigint,
iam_chain_type varchar(30),
PageType tinyint,
IndexLevel tinyint,
NextPageFID tinyint,
NextPagePID int,
PrevPageFID tinyint,
PrevPagePID int
)

TRUNCATE TABLE sp_tablepages;
INSERT INTO sp_tablepages
EXEC ('DBCC IND (test, tblNumbers, 1)');  

--delete the pages of non-leaf levels
delete from sp_tablepages where IndexLevel &lt;&gt; 0 or IndexLevel is null


;with cte as
(
	select PagePID,ROW_NUMBER() over (Order by ID) as ID from sp_tablepages
)
select S.PagepID as ThisFragmentEndPage,SN.PagepID as NextFragmentBeginPage
from cte S
inner join cte  SN on SN.ID  = S.ID + 1 and SN.PagePID &lt;&gt; S.PagePID + 1</pre>

The query might not give the exact count of fragments in the table. In my tests it returned every time 1 or 2 less fragments(probably i am missing something else). This will give the Ending Page Number of the current fragment and First Page Number of Next Fragment. If you look at the values of columns &#8220;ThisFragmentEndPage&#8221;,&#8221;NextFragmentBeginPage&#8221;, for the first few rows they differ by more than 1 page, and for remaining rows, there is exactly one page difference. Interesting fact is that missing one page is actually allocated to table. You can check it in sp_tablepages table. But it is assigned to different Level. 

In the above example, the fragmentation is not because of Page Splits. It is based on he way SQL Storage engine allocates pages while inserting rows.

1. If Table Pages are less than 8, when requesting a new page, Storage Engine will assign a mixed extent. Once it reaches to 8 pages, then only it will start to assign Uniform extent. The reason for this behaviour is, Storage Engine wants to give importance to small tables also. Giving a uniform extent to every table will make lot of pages unused. Small tables will fit within 2-3 pages and these will gain performance from this, when bringing a small extent into cache, will helps both tables become fast. 

2. Even assigning uniform extents will not erase the fragmentation completely. Since Index Non-Leaf Levels also increase along with Leaf Level data, suppose if Page 1000 is assigned to Level 0, if in Level 1, a new page needs to be created, then Storage Engine will assign 1001 to Level1. This will cause the Fragmentation in Level0. If storage engine tries to avoid fragmentation by Assigning a new page from another extent for Level 1 makes the disk move forward and back, which delays the write operations. 

So, if the fragmentation is because of the above 2 reasons, even re-building the index also will not be useful. If the fragmentation is because of Page splits, then re-building index will be useful.

 [1]: http://beyondrelational.com/quiz/SQLServer/General/2010/questions/sqlserver-quiz-general-2010-Sankar-Reddy-What-is-the-reason-for-high-index-fragmentation-even-after-rebuilding-the-clustered-index-sometimes.aspx