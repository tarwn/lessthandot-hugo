---
title: Index DMV usage considerations
author: Ted Krueger (onpnt)
type: post
date: 2010-03-31T12:56:42+00:00
ID: 745
excerpt: 'Index DMV/DMF goodness!  SQL Server 2005 and up has given us the ability to truly be more efficient in gathering information in which we can be more proactive.  With everything these objects give us, a price has to be paid.  We will discuss that price but first, we’ll go over a few major features that DMV/DMF has provided to us in regards to indexes to help us in our daily tasks.'
url: /index.php/datamgmt/datadesign/think-before-you-f5-on-dmvs/
views:
  - 12675
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - dmf
  - dmv
  - index
  - maintenance
  - performance
  - sql server 2005
  - sql server 2008

---
## Let’s think about it for a minute

Index DMV (Dynamic Management Views)/DMF (Dynamic Management Functions) goodness! SQL Server 2005 and up has given us the ability to truly be more efficient in gathering information with which we can be more proactive. With everything these objects give us, a price has to be paid. We will discuss that price but first, we’ll go over a few major features that DMV/DMF has provided to us in regards to indexes to help us in our daily tasks. 

Index maintenance is a major part in the typical day to day DBA tasks. Monitoring fragmentation, usage, and lack of usage is something that, if gone without for long, will cause severe and noticeable performance problems. There is no question about it, Indexes are essential. We cannot afford to ignore embracing them as objects that save poor and even well structured queries from causing crippling resource consumption issues. The user community has little patience for slow applications. Users also will not stand for where the problems are and only want resolution so they can make it from 9 to 5 without headaches. In short, the DBA is a hired gun by the users and as such, are our customers. In this, it is our highest priority to keep the quality of our services as high as you would expect from any sale of services or goods. 

In order to maintain our primary task of keeping indexes from being the problem and always the solution, we have at our disposal in SQL Server 2005+, the sys.dm\_db\_index\_physical\_stats DMV. Prior to SQL Server 2005, finding index usage and statistics was a painful process. Profiler would need to be utilized while capturing execution plans and other pertinent information. Once gathered, would be used in long daunting analysis tasks to determine index creation or maintenance needs. In some cases those tasks ended still in index creation that may not be optimal. 

To use dm\_db\_index\_physical\_stats please refer to Denis Gobo’s ([Blog][1] | [Twitter][2]) blog, “[Finding Fragmentation of an Index and Fixing It][3]”. Denis does a very good job at showing cause and effect of the process of using this DMV in an efficient manner.
  

  
Another SQL community favorite is Michelle Ufford’s ([Blog][4] | [Twitter][5]) script on [finding and resolving index problems][6]. This excellent script that can is an all in one scheduling plan to perform unattended maintenance on index fragmentation.

The next major task a DBA can perform is actually finding indexes that the optimizer ‘thinks’ should be created. We can return missing indexes primarily by using the sys.dm\_db\_missing\_index\_group_stats DMV. This joined to several other key DMVs can show you not only missing indexes, but with a little scripting, actually result in the CREATE INDEX statement itself to create for review. To pick on Michelle again, we can look to her excellent blog, “[A look at missing indexes][7]”. 

The third major key index operation is to find unused indexes. Unused indexes can scream “performance killer”. Not only are these indexes taking up space on disk and backups but they are causing transactions to run slower due to the need to update them along with the primary data. Remember, when you insert/update/delete, you need to maintain all of the indexes. This goes further into maintaining these unused indexes with the first fragmentation task discussed. To find these unused indexes and determine if removing them is appropriate, we have the dm\_db\_index\_usage\_stats DMV. This DMV can return unused indexes based on the sys.indexes system view. This is done by using a NOT EXISTS or other means on the dm\_db\_index\_usage\_stats from the sys.indexes and results in any index that does not have an active log for usage. Hence, is not being fully utilized and should be determined if required. 

## Now for the meat of it and the point

All of this is extremely useful for maintaining, removing and creating indexes. To bring this even further into lazy DBA themes, Jason Strate ([Blog][8] | [Twitter][9]) has created the [IndexAnalysis][10] reporting solution and scripts. Jason has taken the best of all worlds of Index DMV and DMF usage to Reporting Services in a solution that can be easily reviewed for all these major key analysis steps. 

Most DBAs in the community are going to tell you this is all without a doubt, critical knowledge. In SQL forums around the community, they are also posted as quick information gathering tools to obtain information to assist in performance problems. Again, this is all true and the best practice troubleshooting steps. However, there is one major aspect to these types of queries that are also a critical piece of knowledge. That is, these queries themselves are very resource intensive. In order to show that, let’s run one of the typical fragmentation search queries on a test server. 

The below charts are from Idera SQLDM and used only to show a high level view of the effects of the DMVs. Disk IO is focused on as that pertains to one of the largest performance bottlenecks you can have. IO will also starve other normal operations from working optimally. 

The query that will be used is below and part from Denis Gobo’s blog referred to above while removing the direct condition to look at only one index.

sql
SELECT OBJECT_NAME(OBJECT_ID) AS Tablename,s.name AS Indexname
,index_type_desc
,avg_fragmentation_in_percent
,page_count
FROM sys.dm_db_index_physical_stats (DB_ID(), NULL, NULL , NULL, N'LIMITED') d
join sysindexes s ON d.OBJECT_ID = s.id
and d.index_id = s.indid
```

Prior to executing this query, our server was running as follows
  


<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/dmv_idx_1.gif" alt="" title="" width="426" height="161" />
</div>

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/dmv_idx_2.gif" alt="" title="" width="426" height="161" />
</div>

Once executing the query we quickly see extremely high IO. IO is our major concern here. This will slow the server down noticeably and cause other operations to suffer its wrath.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/dmv_idx_3.gif" alt="" title="" width="433" height="161" />
</div>

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/dmv_idx_4.gif" alt="" title="" width="435" height="161" />
</div></p> 

This test was crude and we didn’t go into detail of the IO and exact reactions to running the query on the server. You can see from the charts that the query was killed very quickly. We only wanted to show the start of the resource consumption and overhead. The objective and take away is the knowledge that these queries do in fact have their own resource intensive requirements. When running them due to maintenance, forum support from community members or other means in obtaining them, take care in the time that you do so. In some cases, these queries and other DMVs cannot be stopped until completion. For a truly in-depth and excellent look at usage, check Paul Randal's ([Blog][11] | [Twitter][12]) blog, “[Inside sys.dm\_db\_index\_physical\_stats][13]“.

It is much more optimal when executing these queries during normal business hours if a query can be singled out as a performance problem. This provides the ability to select only the statistics for one index or one table. When obtaining help, always question the queries provided by any of the members helping. No offense will be taken by the SQL community supporters if questions are asked of the performance factor of running provided scripts during normal operating times. If offense it taken, this typically means the impact is unknown and more reason to question it. 

**Special thanks to Jes ([Blog][14] | [Twitter][15]) for the review 🙂**

 [1]: /index.php/All/?disp=authdir&author=4
 [2]: http://twitter.com/denisgobo
 [3]: /index.php/DataMgmt/DataDesign/finding-fragmentation-of-an-index-and-fi
 [4]: http://sqlfool.com
 [5]: http://twitter.com/sqlfool
 [6]: http://sqlfool.com/2009/06/index-defrag-script-v30/
 [7]: http://sqlfool.com/2009/04/a-look-at-missing-indexes/
 [8]: http://stratesql.com/
 [9]: http://twitter.com/stratesql
 [10]: http://stratesql.com/2009/07/20/analyzing-your-indexes-with-a-custom-report.aspx
 [11]: http://www.sqlskills.com/BLOGS/PAUL/
 [12]: http://www.twitter.com/PaulRandal
 [13]: http://www.sqlskills.com/BLOGS/PAUL/post/Inside-sysdm_db_index_physical_stats.aspx
 [14]: http://jesborland.wordpress.com
 [15]: http://twitter.com/grrl_geek