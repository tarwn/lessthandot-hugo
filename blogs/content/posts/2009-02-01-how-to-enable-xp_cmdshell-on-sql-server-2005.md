---
title: How to enable xp_cmdshell and Ad Hoc Distributed Queries on SQL Server 2005
author: SQLDenis
type: post
date: 2009-02-01T12:06:12+00:00
ID: 307
url: /index.php/datamgmt/datadesign/how-to-enable-xp_cmdshell-on-sql-server-2005/
views:
  - 43505
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - openquery
  - openrowset
  - security
  - sql server 2005
  - sql server 2008
  - xp_cmdshell

---
If you try to execute xp_cmdshell on a fresh install of SQL Server 2005 or 2008 you will be greeted with the following message

_Server: Msg 15281, Level 16, State 1, Procedure xp_cmdshell, Line 1
  
SQL Server blocked access to procedure &#8216;sys.xp\_cmdshell' of component &#8216;xp\_cmdshell' because this component is turned off as part of the security configuration for this server. A system administrator can enable the use of &#8216;xp\_cmdshell' by using sp\_configure. For more information about enabling &#8216;xp_cmdshell', see “Surface Area Configuration” in SQL Server Books Online._

This is done for security reasons. I see a bunch of Google searches hitting our site with a search like this: How do I enable xp_cmdshell in SQL Server 2005? Well you can do it two ways; one is with a script and the other with the Surface Configuration Tool
  
Let's start with the Surface Configuration Tool

**Surface Configuration Tool**
  
You have to navigate to the tool from the start button, the path is below
  
Programs&#8211;>Microsoft SQL server 2005&#8211;>Configuration Tools&#8211;>Surface Configuration Tool

Then select Surface Area Configuration for Features (bottom one)
  
Expand Database Engine go all the way down to xp\_cmdshell and click enable xp\_cmdshell and hit apply

**SQL Script**
  
To do it with a script use the one below

sql
EXECUTE sp_configure 'show advanced options', 1
RECONFIGURE WITH OVERRIDE
GO

EXECUTE sp_configure 'xp_cmdshell', '1'
RECONFIGURE WITH OVERRIDE
GO

EXECUTE sp_configure 'show advanced options', 0
RECONFIGURE WITH OVERRIDE
GO
```

Just so you know you can also use the script to enable xp_cmdshell on SQL Server 2008. The Surface Area Configuration Tool in SQL Server 2008 has been replaced by the SQL Server Configuration Manager.

In SQL Server 2005 and 2008 OPENROWSET is also disabled by default, if you try to run an OPENROWSET query then you will see the following message:

_Server: Msg 15281, Level 16, State 1, Line 1
  
SQL Server blocked access to STATEMENT &#8216;OpenRowset/OpenDatasource' of component &#8216;Ad Hoc Distributed Queries' because this component is turned off as part of the security configuration for this server. A system administrator can enable the use of &#8216;Ad Hoc Distributed Queries' by using sp_configure. For more information about enabling &#8216;Ad Hoc Distributed Queries', see “Surface Area Configuration” in SQL Server Books Online._

To enable OPENROWSET and OPENQUERY you can use the previous script but instead of &#8216;xp_cmdshell' you will use &#8216;Ad Hoc Distributed Queries'. The script to enable Ad Hoc Distributed Queries is below

sql
EXECUTE SP_CONFIGURE 'show advanced options', 1
RECONFIGURE WITH OVERRIDE
GO
 
EXECUTE SP_CONFIGURE 'Ad Hoc Distributed Queries', '1'
RECONFIGURE WITH OVERRIDE
GO
 
EXECUTE SP_CONFIGURE 'show advanced options', 0
RECONFIGURE WITH OVERRIDE
GO
```



\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][2] forum**<ins></ins>

 [1]: http://forum.ltd.local/viewforum.php?f=17
 [2]: http://forum.ltd.local/viewforum.php?f=22