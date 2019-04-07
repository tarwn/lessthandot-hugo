---
title: T-SQL Tuesday – Give me my cores SSIS 2008!
author: Ted Krueger (onpnt)
type: post
date: 2010-06-08T08:39:46+00:00
ID: 813
excerpt: 'I’m jumping into the T-SQL Tuesday fun this week.  It is a busy week at that with SQL University writing and everything going on in the SQL Community.  The SQL Server 2008 (R2) hottest, most favorite new feature topic had me wanting to throw SSIS out there once more and show off the Data Flow Engine changes.'
url: /index.php/datamgmt/dbprogramming/ssis-2008-multiproc-usage/
views:
  - 14630
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - sql community
  - sql server 2008
  - sql server 2008 r2
  - sql server integration services
  - ssis
  - ssis 2008

---
 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/sqltuesday1.gif" alt="" title="T-SQL Tuesday" width="154" height="154" align="left" />
</div></a>I’m jumping into the T-SQL Tuesday fun this week. The very well known, Jorge Segarra ([blog][1] | [twitter][2]) is [hosting the fun][3] this time around. It is a busy week at that with SQL University writing and everything going on in the SQL Community. The SQL Server 2008 (R2) hottest, most favorite new feature topic had me wanting to throw SSIS out there once more and show off the Data Flow Engine changes. OK, the Data Flow Engine isn&#8217;t a, &#8220;New Feature&#8221; but given the redesign, I&#8217;m throwing it into the mix with the rest. This was a big, big and did I say BIG change in SSIS 2008. Being a performance freak, the changes to the Data Flow and the effective use of multi-core processors was what had me the most excited. See, with SSIS 2005, Data Flow was sent off on its merry way running execution trees with only one lonely execution thread. That meant one thread! This doesn’t help us much in a core happy world we live in. So in SSIS 2008 we had a big change to this architecture. Did I mention this was big? ### **Off to the races we go with Pipeline Parallelism**What SSIS 2008 has given us over SSIS 2005 and the execution tree is the ability to run more than one component from the single tree. This is really a huge change. Before this change and automated thread scheduling in SSIS, we had to try making our own with designing methods. In some cases the changes in designs caused other performance problems themselves. Now with a thread pool, threads are assigned dynamically (yes, auto-magically) to components. Thread pooling is not a new concept to the computing world (and .NET framework). Thread pooling manages where and what work a set of threads will work on. When work is thrown at the pool, the work is sent off to threads that can work on it. This means multiple threads working on multiple jobs in parallel execution. Thread pooling can be done manually but with SSIS and SQL Server, we like the concept of this being controlled without us causing problems. In SSIS 2008 we were given just that automatic scheduling.The question of the hour is, does this help us with performance? The performance shines from the massive tests that have been done already and can answer the question for us. If you haven’t heard of the [ETL World Record][4], you need to get over there and check it out. 1 TB in 30 minutes!! Yes, on SSIS 2008 and the architecture behind it. The best way to check this out is to show the differences. Robert Sheldon also went over this test on the, [SSIS 2008 Crib sheet][5]. Very cool write-up and recommended reading it.### **The truth is in the pipe by logging it**Let’s create a package that will run some data through so we can log the execution.> <span class="MT_red">Note: the SSIS 2008 Crib sheet shows pretty much the same here. Robert did an awesome job explaining all of this. Highly recommend reading it.</span>Steps to the test SSIS in 2005 and 2008 so we can see the execution changes and speed differences   1. In BIDS 2005, create a new package named Pipes 2005  2. Bring over an OLE DB Source and connect this source to AdventureWorks.   3. Bring in the table, Sales.SalesOrderDetail  4. Drop a Conditional Split into the Data Flow tab  5. Make the condition based on OrderQty being greater than one. <div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/sqltuesday2.gif" alt="" title="" width="628" height="76" />
</div>  6. Next, drag and drop two Derived Column’s  7. Make one Derived column the Case 1 output by connecting the first Path to it and selecting Case 1 output  8. Make the Expression (DT\_STR,3,1252)ProductID + &#8221; for &#8221; + (DT\_STR,3,1252)OrderQty  9. Change the Data Type to DT_STR (non-unicode string)<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/sqltuesday3.gif" alt="" title="" width="628" height="112" />
</div> 10. Do this for the next Derived Column by connecting the remaining output path to it.  11. Name the column Under2orders and leave the Expression the same 12. Connect both Derived Columns to two unique SQL Server Destinations 13. In the SQL Server Destinations, use the same connection of AdventureWorks. Click, New to create a new table. Use the following CREATE TABLE statement: sql
CREATE TABLE [Playing_1] (
    OrderOver1Unit varchar(10)
    )
```
 14. Use this for the second destination but name the table, [Playing_2]Our finished product should appear like below<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/sqltuesday4.gif" alt="" title="" width="444" height="381" />
</div> 15. Next, click the menu option SSIS and select Logging 16. Select package in the Containers and add a new SSIS log provider for Text files. 17. Add a path to the new log of C:ExecutionTreeSSIS_Test2005 18. Highlight Data Flow Task to move into logging the task. 19. Click Details in the right tabs and click the Advanced button  20. Scroll down a bit and check PipelineExecutionTree.  21. Uncheck everything except MessageText<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/sqltuesday5.gif" alt="" title="" width="628" height="317" />
</div> 22. Click OK until out of the editors and save the package.  23. Run the package from BIDS <div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/sqltuesday6.gif" alt="" title="" width="474" height="409" />
</div>And we have our data split and loaded<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/sqltuesday7.gif" alt="" title="" width="380" height="419" />
</div>Execution time was 936 MillisecondsSSIS 2005 PipelineExecutionTrees log> begin execution tree 0
     
> output &#8220;OLE DB Source Output&#8221; (11)
     
> input &#8220;Conditional Split Input&#8221; (17)
     
> output &#8220;Case 1&#8221; (146)
     
> input &#8220;Derived Column Input&#8221; (163)
     
> output &#8220;Derived Column Output&#8221; (164)
     
> input &#8220;SQL Server Destination Input&#8221; (61)
     
> output &#8220;Derived Column Error Output&#8221; (165)
     
> output &#8220;Conditional Split Default Output&#8221; (18)
     
> input &#8220;Derived Column Input&#8221; (169)
     
> output &#8220;Derived Column Output&#8221; (170)
     
> input &#8220;SQL Server Destination Input&#8221; (78)
     
> output &#8220;Derived Column Error Output&#8221; (171)
     
> output &#8220;Conditional Split Error Output&#8221; (20)
  
> end execution tree 0
  
> begin execution tree 1
     
> output &#8220;OLE DB Source Error Output&#8221; (12)
  
> end execution tree 1We can see all of the work was primarily done in execution tree 0. 936 Milliseconds isn’t the greatest for what we just did either.Now upgrade the package to 2008 by creating a new SSIS project. Right click SSIS Packages in the Solution Explorer and select Add Existing. Browse to the package we just created in SSIS 2005 (found in your projects folder in My Documents by default). Double click the package to bring it in.
  
You will receive the succeeded upgrade to SSIS 2008 message. Click OK to close it and load the upgraded 2008 package.Click the SSIS option in the menu strip and select lopping. Change the path to the text file to be C:ExecutionTreeSSIS_Test2008
  
Save and run the packagePackage execution time for this was 640 Milliseconds. Now check the log
  
> ExecutionTreeSSIS_Test2008</p> Begin path plan
     
> Begin Path Plan 0
        
> Call ProcessInput on component &#8220;Conditional Split&#8221; (16) for input &#8220;Conditional Split Input&#8221; (17)
        
> Create new execution item for subpath 0
        
> Create new execution item for subpath 1
        
> Begin Subpath Plan 0
           
> Create new row view for output &#8220;Case 1&#8221; (146)
           
> Call ProcessInput on component &#8220;Derived Column&#8221; (162) for input &#8220;Derived Column Input&#8221; (163)
           
> Create new row view for output &#8220;Derived Column Output&#8221; (164)
           
> Call ProcessInput on component &#8220;SQL Server Destination&#8221; (45) for input &#8220;SQL Server Destination Input&#8221; (61)
        
> End Subpath Plan 0
        
> Begin Subpath Plan 1
           
> Create new row view for output &#8220;Conditional Split Default Output&#8221; (18)
           
> Call ProcessInput on component &#8220;Derived Column 1&#8221; (168) for input &#8220;Derived Column Input&#8221; (169)
           
> Create new row view for output &#8220;Derived Column Output&#8221; (170)
           
> Call ProcessInput on component &#8220;SQL Server Destination 1&#8221; (62) for input &#8220;SQL Server Destination Input&#8221; (78)
        
> End Subpath Plan 1
     
> End Path Plan 0End path planChanges we can see in the logging are the Paths and the Subpaths. This is showing the tasks being executed in the main path in parallel by using Subpaths. And our execution time was increased. ### **Where’s the beef? Right there, in the Data Flow Engine**The Data Flow Engine changes are truly an enhancement that pushes SSIS to the Enterprise ETL levels. Don’t sell yourself short by dismissing the abilities of SSIS as anything less than performing at that level. > References and in-depth reading materials
  
> [Architecture of Integration Services][6][Top 10 Performance and Productivity Reasons to Use SQL Server 2008 for Your Business Intelligence Solutions][7][SSIS 2008 Crib Sheet][5]

 [1]: http://sqlchicken.com/
 [2]: http://twitter.com/sqlchicken
 [3]: http://sqlchicken.com/2010/06/t-sql-tuesday-007-summertime-in-the-sql/
 [4]: http://blogs.msdn.com/b/sqlperf/archive/2008/02/27/etl-world-record.aspx
 [5]: http://www.simple-talk.com/sql/ssis/sql-server-2008-ssis-cribsheet/
 [6]: http://msdn.microsoft.com/en-us/library/bb522498.aspx
 [7]: http://sqlcat.com/top10lists/archive/2009/02/24/top-10-performance-and-productivity-reasons-to-use-sql-server-2008-for-your-business-intelligence-solutions.aspx