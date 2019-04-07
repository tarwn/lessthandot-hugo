---
title: 'SQL Advent 2012 Day 13: Features enabled that are not used'
author: SQLDenis
type: post
date: 2012-12-13T10:14:00+00:00
ID: 1851
excerpt: 'This is day thirteen of the SQL Advent 2012 series of blog posts.  Today we are going to look at servers where everything is installed and enabled.'
url: /index.php/datamgmt/dbprogramming/features-enabled-that-are-not/
views:
  - 9366
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - features
  - sql advent 2012
  - sql server 2008
  - sql server 2008 r2
  - sql server 2012
  - ssms

---
This is day thirteen of the [SQL Advent 2012 series][1] of blog posts. Today we are going to look at servers where everything is installed and enabled. Before we start this post let&#8217;s look back in time a little. Before SQL Server 2005 came out when you installed SQL Server pretty much everything was turned on by default. This of course widened the attack vector against the database servers. With SQL Server 2005 pretty much everything is turned off and you have to turn the features on if you want to use them. Now sometimes some admins will just turn everything on because that way they don&#8217;t have to deal with this later, this are also the same kind of people who insist that the account needs to be db_owner otherwise their code won&#8217;t work.

To see what these features are and if they are turned off, you can use the following query. 

sql
SELECT name, value,value_in_use 
FROM sys.configurations
WHERE name IN (
'Agent XPs',
'SQL Mail XPs',
'Database Mail XPs',
'SMO and DMO XPs',
'Ole Automation Procedures',
'Web Assistant Procedures',
'xp_cmdshell',
'Ad Hoc Distributed Queries',
'Replication XPs',
'clr enabled')
```

The difference between _value_ and _value\_in\_use_ is that _value\_in\_use_ is what is currently used and _value_ will be used next time the server is restarted. If you want to have the change take effect immediately then use RECONFIGURE

Here is an example that will turn off xp_cmdshell

sql
EXECUTE sp_configure 'show advanced options', 1
RECONFIGURE
GO
 
EXECUTE sp_configure 'xp_cmdshell', '1'
RECONFIGURE
GO
 
EXECUTE sp_configure 'show advanced options', 0
RECONFIGURE
GO
```

If you prefer the GUI, you can also use that, right click on the database server name in SSMS, select Facets, from the Facet dropdown select Surface Area Configuration. You will see the following Facet properties

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/ADvent/Facets.PNG?mtime=1355339984"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/ADvent/Facets.PNG?mtime=1355339984" width="665" height="531" /></a>
</div>

Here you can enable or disable the features you are interested in. You can also export these properties as a policy to use on other servers so that the features are the same on all your servers

## Installing everything by default

Here I have mixed feelings myself about what to do. On one hand I don&#8217;t like to install SSAS or SSRS if there is no need for it, on the other hand I don&#8217;t feel like adding that stuff 6 months down the road if there is suddenly a need for it. If I do install it, I make sure it at least doesn&#8217;t run by default but it is disabled. There is no benefit in having SSAS, SSRS or SSIS running and using CPU cycles as well as RAM if nobody is using these services. If you do install it and nobody uses it, disable the services, you will have more RAM and CPU cycles for the SQL Server service available.

That is all for day thirteen of the [SQL Advent 2012 series][1], come back tomorrow for the next one, you can also check out all the posts from last year here: [SQL Advent 2011 Recap][2]

 [1]: /index.php/DataMgmt/DBProgramming/sql-advent-2012-here-is
 [2]: /index.php/DataMgmt/DataDesign/sql-advent-2011-recap