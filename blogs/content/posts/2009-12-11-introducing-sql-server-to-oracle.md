---
title: Introducing SQL Server to Oracle
author: Ted Krueger (onpnt)
type: post
date: 2009-12-11T13:37:21+00:00
ID: 646
url: /index.php/datamgmt/datadesign/introducing-sql-server-to-oracle/
views:
  - 10720
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - IBM DB2
  - IBM DB2 Admin
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
  - MySQL
  - MySQL Admin
  - Oracle
  - Oracle Admin

---
Over the last few weeks I have been working on the task of bringing an Oracle database into my SQL Server landscape. The basic process that needs to be accomplished is to get this Oracle database pumped into the existing SQL Server structure primarily to address the reporting aspect and requirements. The software that utilizes the Oracle database could easily sit in their own little world without much need for us to go outside that shell but given my completely SSRS reporting structure, there is a need to build off the new data store for the users taking into account the completely native SQL Server backend.

Oracle can easily bring itself into SSRS as a data source. In fact I highly recommend only doing that if you find yourself with this task. The company I currently work with however, require that the data be backed up, available in all sources and backed up again. To resolve the businesses needs I chose to bring the entire Oracle database into a SQL Server database nightly so the data was readily available and easily accessed by the skills of my group. In my experience as a SQL Server professional, when Oracle is introduced into a SQL Server environment, the last thing you want to do to your developers and Jr. DBAs is install the Oracle client on all their machines and then throw SQL*Plus at them. There is a reason that it's hard to find cross dressing Oracle/SQL Server Experts. They mix like UNIX and Windows OS.

The major key that will adjust the process of how you obtain a complete extract from Oracle to SQL Server is the size of data and the network structure. One thing you should think about prior to starting the actual setup of this type is what data do you really need? In the case when there is a set based data result you can export in a 24 hour period by date, you can look at this process as a type of wannabe warehouse ETL process. Use SQL Server as your holding tank in which you retain the critical data that is required to recover and supply the business with the data. SQL Server Integrated Services is a tool that can quickly get you up and running on the type of export I'm outlining above. With SSIS you can work with the Oracle design to bridge the data into a SQL Server design. This gives you an upper hand over writing queries that may not be efficient in T-SQL to extract data for reporting by means of doing the conversion to the SQL Server design at export/import stages.

Now that I've laid down the plan, I want to make one hardened statement. "Never install the Oracle client on a production database server that holds other databases that are critical to the company".

You may be asking why I say that without much room for the maybe situations. Here are the facts. You are probably reading this because you are a SQL Server shop and have little to no Oracle experience or an Oracle DBA on hand. In my experience, the Oracle client is not an easy task for SQL Server DBAs to plug in and get going. I have done this 4 times in my career and still have a hard time with it. There are basic steps involved to get Oracle working from remote servers. Install the client, setup TNSNAMES and a few other minor requirements just to get a functioning SQL*Plus session. On each installation those steps never went the same and were never direct for me. The reasoning behind this is, we as SQL Server DBAs are not Oracle infrastructure experts. Spending a day reading up on how the client and Oracle instance work together on Windows (possibly to UX) is not efficient to call anyone an expert on any process. Don't approach this task as if you are. It has failure and hardships written all over it. 

If you have the resources and budget available, create a job server installation that can assist in making the implementation of the Oracle instance onto your SQL Server instances easier to manage and troubleshoot. Job servers are something I push for in all environments for SQL Server. There is performance factors that come to mind when putting major job tasks on production database server that have the task of serving data to the business. It is an easy resolution to a growing or already large integration services group to move them off to an instance that is designated and configured to handle the different needs of SSIS and what you are doing with it.

So even with my poor diagramming abilities, you can see the safety barrier between the two different systems. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//job_diagram.gif" alt="" title="" width="540" height="480" />
</div>

Don't get me wrong on everything I've written either. I am not an Oracle hatter. Yes, I have chosen the SQL Server DBA path because it's just that much better (:P). I have worked on DB2, Oracle and SQL Server. All have their strengths and all have weaknesses. 

When the time comes for you to think about making them all work together, I hope this short blog of a possible mix will help you get the gears going. It's critical in the process of integrating different database servers into your environment to ensure they do not affect the abilities of them working optimally.