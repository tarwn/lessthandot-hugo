---
title: Backup file contents with SSIS – Defining the Design
author: Ted Krueger (onpnt)
type: post
date: 2011-01-24T11:03:00+00:00
excerpt: 'SSIS has given SQL Server a powerful and extremely useful ETL platform.  With this ETL platform we have also gained the ability to build packages that can perform higher level tasks that were once impossible or very difficult in SQL Server.  To accomplish some tasks, external executable, scripts and interfaces were development while only having the ability o utilize the SQL Server Agent as a scheduling system.  Tasks such as automated backup and restore, file services, security audits and monitoring were all done with those methods but now can be encapsulated directly in SSIS and thus, held directly within out SQL Server environments.'
url: /index.php/datamgmt/datadesign/ssis-defining-the-design-1/
views:
  - 4547
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Microsoft SQL Server
  - SSIS

---
This four part series of articles will go over using SQL Server Integration Services (SSIS) to show how to perform the process of backing up critical files such as config, ini, xml and txt extensions. While we design and discuss the objects that are utilized, the concept of automating heavy administrative tasks will be defined. To perform the tasks of backing up critical files, the Foreach Container, Script Task, Execute SQL Task, Data Flow with the use of Lookup, Conditional Splits and the OLEDB Command will be shown as useful tasks and components to achieving that automation. 

Files critical to any application, service or operating system that directly control functionality are as critical as the data that is stored in SQL Server databases. An excellent example of this is the SQL Server Reporting Services (SSRS) configuration files. These configuration files are critical to SSRS functioning correctly from the Report Server to the ability to export and save reports into other formats. To proactively go to SSRS instances and back them up, alter them and track their changes is a cumbersome and time consuming task. We will be taking the cumbersome and time consuming aspects out of retaining backups of these configuration files by using SSIS as an automation tool. In the process of developing this solution, solutions for change tracking and exception handling will appear. With any design, as you work through the development, opportunities such as these arise. These opportunities can be extremely beneficial to adding to the design but also must be taken into consideration and not change the path of the design in itself. 

**The first task of any development process begins with the design.** 

For our design we want to create a workflow to define our needs. In all, the work flow involves the looping through an open directory (and subdirectories) while pulling files out of them and inserting them into a database.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-10.png?mtime=1295736729"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-10.png?mtime=1295736729" width="167" height="167" align="left" /></a>
</div>

As mentioned earlier, SSIS provides us with all the tasks and components to achieve this complete process. They have been identified as follows:

  1. For Each Loop Container 
  2. Script Task – providing high level assembly access
  3. Execute SQL Task – T-SQL commands to SQL Server
  4. Data Flow – SSIS foundation for data in and out
  5. Lookup – Taking identifiers and searching sources for matches
  6. Conditional Split – Ability to use expressions to act on data
  7. OLE DB Command – Issuing statements within the Data Flow task for each row it receives

From the generic image above a workflow begins to take shape. Flow charting this workflow allows us to single out each step and process that needs to be developed. Not only does it allow us to make this identification but it provides us with focus while not losing the direction that has been defined. That is not to say that a definition and flow chart is set in stone. These can and typically will develop over the process of creating the actual system from these tools. As long as the scope is not lost with these alterations to the definition, this can be a powerful designing practice.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-11.png?mtime=1295736730"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-11.png?mtime=1295736730" width="624" height="361" /></a>
</div>

With this flow we achieve the major tasks: Get the file information, decision to update or insert and then the final cleansing of what the process itself has created.

In SSIS we have the unique ability to show a DTSX package from Business Intelligence Studio (BIDS) as a work flow picture or image of the process. This is simply done by showing the partial or completed view of the tasks and components utilized from BIDS. BIDS and SSIS development have distinct icons for each object that are commonly known or searched on. These symbols provide the understanding of the finished design and flow.

**For our task this appears as follows.**

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-12.png?mtime=1295736731"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-12.png?mtime=1295736731" width="607" height="371" /></a>
</div>

Of course designing a package is the second to last step just before testing your development efforts. The above illustration is given to show the comparison to your workflow creation and how it can be viewed in a finished product from BIDS. 

In our design we have identified the tasks and components we will need to accomplish the design, the actual processing steps and focus points that we will need to develop and a final defining view of what we’re looking to achieve. Research to accomplishing this falls into line next. With experience, simple and complex tasks can be common knowledge to an individual performing the actual development. More commonly, questions come up that require the needs to look for the bests answer. The above designing phases allow those questions to be uncovered and answered long before finding out that one must start over due to a mistake or assumed ability of one particular task. 

**Closing part 1**

A word of automation: SSIS has given SQL Server a powerful and extremely useful ETL platform. With this ETL platform we have also gained the ability to build packages that can perform higher level tasks that were once impossible or very difficult in SQL Server. To accomplish some tasks, external executable, scripts and interfaces were development while only having the ability o utilize the SQL Server Agent as a scheduling system. Tasks such as automated backup and restore, file services, security audits and monitoring were all done with those methods but now can be encapsulated directly in SSIS and thus, held directly within out SQL Server environments. 

In this series we will show only one process that will take advantage of SSIS to automate seemingly cumbersome operations of backing up files that are critical to our systems. Over the process of showing this, several SSIS techniques and methods will be covered. These will be highlighted in their own parts to the series as outlined below:

Part 1: Defining the design
  
Part 2: Foreach Loop Container and file handling
  
Part 3: Tables, staging areas and supporting SSIS with Stored Procedures
  
Part 4: Data Flow – INSERT/UPDATE Decisions