---
title: Running older compatibility levels in newer versions
author: Ted Krueger (onpnt)
type: post
date: 2010-12-28T15:18:38+00:00
excerpt: 'One very sound argument that many DBAs have for pushing an upgrade of SQL Server is the fact that you have the ability to leave a database in an older compatibility level.  In some ways, compatibility levels in a SQL Server database govern the feature set that the database will utilize.  SQL Server will treat the database as though it is still being hosted on the server version that matches the compatibility level the database has been left in.'
url: /index.php/datamgmt/dbadmin/upgrading-comp-level-sql-server/
views:
  - 16108
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - database administration
  - sql server 2008
  - sql server upgrade

---
<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/comp_level.gif" alt="" title="" width="121" height="91" align="left" />
</div>

One very sound argument that many DBAs have for pushing an upgrade of SQL Server is the fact that you have the ability to leave a database in an older compatibility level. In some ways, compatibility levels in a SQL Server database govern the feature set that the database will utilize. SQL Server will treat the database as though it is still being hosted on the server version that matches the compatibility level the database has been left in. 

**Not faulting them for saying no**

In some cases, having this ability to leave a database running in an older state is extremely useful. This is seen in cases when a team has set the initiative for upgrading SQL Server to a higher release. A hardship of DBAs is the fact that we are governed on our database servers by the databases that are hosted on them; this being set by the vendors and even in house designed databases and their ability to upgrade. Allowing compatibility levels to remain at older versions while still upgrading binary support of SQL Server allows us, to a certain extent, the ability to push upgrades to SQL Server, while not completely disabling a database that may not take advantage of new functionality. In many cases the lack of support for upgrading SQL Server databases is set forth due to the resources of the vendors and in house custom databases not fully testing the upgraded databases. In this case the, “Better safe than sorry” term is taken to heart.

Some key facts that may cause a compatibility upgrade to cause problems are changes in how SETs are handled, the removal of the \*=, =\* to requiring OUTER JOIN syntax and an extremely common issue is the use of SET XACT\_ABORT OFF in triggers to prevent failing entire batches based on an error raised by a single transaction. In SQL Server 2000, SET XACT\_ABORT OFF was allowed in triggers while higher versions do not allow this. This was an extremely bad practice that was used in order to prevent triggers from causing entire operations from rolling back. While this may sound like a good idea, it can cause integrity issues and data that is incomplete.

Every business has to manage resources and those resources may be placed in an area where the business initiative is better at that time. In saying that, you cannot fault the fact that these databases may not fully support an upgrade from, say, compatibility level 80 to 90. What we can do is work with the business, describing the upgrade paths of allowing a database to stay in an older compatibility level, while we take full advantage of upgrading the server for other data services. 

**The other side**

At the same time, we as DBAs have the ability to bring a case to the whiteboard for a database to be brought to a higher compatibility level.

Recall that we mentioned that a database left in an older compatibility level simply does not take advantage of SQL Server and new features. Upgrading SQL Server and any other system at this level brings several advantages. For example, take the introduction of Data Management Views and Data Management Functions. With SQL Server 2005 or compatibility level 90, SQL Server introduced DMV/DMFs into the administration area. DMV/DMFs have brought with them a new heightened level to all aspects of administering SQL Server. Some examples are Resources utilization, Query performance, Session management and even real-time advanced troubleshooting for events that otherwise would force us to run resource-intense processing such as Profiler or Performance Monitor, simply to determine one area that is hindering the data services from performing to the best of their abilities. Before you throw your hands up and say DMVs and DMFs are available in compatibility level 80, please read on. We can take advantage of DMVs and DMFs in compatibility level 80 but run into problems that otherwise would not be an issue when utilizing them. This is later covered in a link to a post by Paul Randal as part of an excellent series on myths in SQL Server. One thing pointed out is the use of OBJECT\_ID with a 3-part name passed in for the object name. Other things that do not work are DMFs such as the helpful sys.dm\_exec\_sql\_text(). Using this DMF in compatibility level 80 results in a syntax error over a meaningful error and may lead to lengthy searches for syntax problems when the DMF is simply not supported. This DMF also brings up the topic that Jonathan Kehayias ([Twitter][1] | [Blog][2]) blogged about on an [in place upgrade of SQL Server to 2008][3] and the master database being left in 80 compatibility. 

Most functionality can be done by passing queries through a database that is 90+ compatibility levels but is a workaround that can be avoided by upgrading the levels. Forming well written code is essential to our database servers. This goes for application level query support and administrative level support. If we write code that is matched to the version including reserved words, functions, syntax related changes and operational commands, we leave ourselves in a state where upgrades will be less of a hassle and more manageable.

So, we know that we can utilize things like DMVs but we lengthen the time in developing with them, along with not writing queries that are formed using best for the version we are working on. Of course we could handle this and keep pushing these changes as we go. Unfortunately, this line of thought commonly ends in hitting a wall at some point, and forces a vast amount of energy to be put into major changes when the overall changes of moving forward over time could have prevented them. This all equates to money saved on resources and providing better products.

There is a great disconnect, if you will, from the new version of SQL Server and enhancements made with the database left in a compatibility level that does not match the server engine. This may cause the database processing to not take full advantage of how the upgraded changes have been set in the engine. This may or may not cause a performance change in your database. In most cases, it will not be a noticeable performance problem. In larger and higher transactional databases, you may see an improvement in moving to the new compatibility level. When we are in the situations with high-end transactional database servers, every performance increase that we can take advantage of should be accepted. This is the reason we upgrade at all.

**Why not just leave it then?**

After going through all of the advantages and disadvantages to leaving compatibility levels that do not match the SQL Server base compatibility, you may be asking yourself why even bother upgrading at all. The problem with allowing the older compatibility levels to remain is the fact that SQL Server upgrades will persist and those upgrades drop support for later versions as they move forward. At the time of this article, SQL Server 2008 only will go back to SQL Server 2000 compatibility level of 80.

There is a sound argument in asking for a database to be tested in a newer compatibility level by simply staying with the growth and advancements of SQL Server. Testing will expose some bad practices that can be fixed, utilization of enhanced T-SQL statements, writing reusable and portable code to other databases across your landscape and possible performance gains with allowing the newer versions of SQL Server to handle databases.

Resources that should be reviewed when determining if you should upgrade your compatibility level on SQL Server:
  
[DMV/DMF on Compatibility level 80 – Paul Randal][4] ([Twitter][5] | [Blog][6])
  
[Listing of what you lose and gain (and vice versa) &#8211; BOL][7]
  
[Backward compatibility &#8211; BOL][8]
  
**Should I flip the switch?**

At this point you’ve decided to either change your compatibility level to match the currently running SQL Server level. Changing the compatibility level is a simple process and does not alter actual data that is stored in the database. Since this change is looked at as quick and painless, it is commonly recommended to simply go ahead and make the change at any given time. There are considerations that must be taken into account though before you change the compatibility level. Again, changing the compatibility level brings the database into a level that fully utilizes the current level of the engine. This may change the results while transactions are processing. This is part in the possibility of the plan being based on the old and new compatibility levels. Making a compatibility level change should be done when there are no active user sessions on the database. Another potentially high impact effect of changing the compatibility level is the event forcing a recompile of all procedures in the database. Unnecessary recompile events can cause performance deterioration due to consumption of more resources and time of execution.

Before changing your compatibility level, check the link on the ALTER DATABASE statement for making the change itself. There is a best practice section in which BOL describes the steps to take when upgrading the level.

 [1]: http://twitter.com/sqlsarg
 [2]: http://sqlblog.com/blogs/jonathan_kehayias/default.aspx
 [3]: http://sqlblog.com/blogs/jonathan_kehayias/archive/2010/05/04/master-database-compatibility-level-after-an-in-place-upgrade.aspx
 [4]: http://www.sqlskills.com/BLOGS/PAUL/post/A-DBA-myth-a-day-(1330)-you-cannot-run-DMVs-when-in-the-80-compat-mode-(T-SQL-Tuesday-005).aspx
 [5]: http://twitter.com/paulrandal
 [6]: http://www.sqlskills.com/blogs/paul/
 [7]: http://msdn.microsoft.com/en-us/library/ms178653.aspx
 [8]: http://technet.microsoft.com/en-us/library/cc707787.aspx