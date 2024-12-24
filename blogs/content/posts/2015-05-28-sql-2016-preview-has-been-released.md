---
title: SQL 2016 Preview has been released!
author: Koen Verbeeck
type: post
date: 2015-05-28T10:54:51+00:00
ID: 3394
url: /index.php/datamgmt/dbprogramming/mssqlserver/sql-2016-preview-has-been-released/
views:
  - 28787
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server
tags:
  - ctp2
  - sql server
  - sql2016
  - ssis
  - ssrs
  - syndicated

---
For those who haven't noticed already:

SQL Server 2016 CTP2 (or Preview as some call it) has just been released!
  
This means it is play time! 🙂

You can either download a copy if you're old school like me, or you can spin up a VM in Azure like all the cool kids.

[SQL Server 2016 first public preview now available!][1]

Some of my favorite features (although some are not yet in the preview):

  * SSIS incremental deployment model (aka deploy only 1 package to the SSIS catalog). Although I understand the reason of the SSIS team to first allow only deployments of full projects, there are a lot of customers that want this functionality.
  * Power Query in all the things! (more specifically: in SSIS and SSRS)
  * R integration in SSRS (this should allow you to do amazing things with SSRS)
  * SSRS revamp (still have to see it though, but I'm already excited!)
  * [Temporal tables ][2]
  * [Live query plans][3] (seems pretty awesome)
  * DAX enhancements (many 2 many, median, percentiles, and so on)

Here's a good overview of what you can expect in SQL Server 2016:

[BI on your terms with SQL Server 2016][4]

Be careful, if you install Polybase, you apparently need the Oracle JRE. Boooo.

[<img class="alignnone size-full wp-image-3395" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2015/05/polybase_jre.jpg" alt="polybase_jre" width="594" height="285" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2015/05/polybase_jre.jpg 594w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2015/05/polybase_jre-300x143.jpg 300w" sizes="(max-width: 594px) 100vw, 594px" />][5]

Anyway, a lot of goodness and I am very excited about this release.

&nbsp;

 [1]: http://blogs.technet.com/b/dataplatforminsider/archive/2015/05/27/sql-server-2016-first-public-preview-now-available.aspx
 [2]: https://msdn.microsoft.com/en-us/library/dn935015(v=sql.130).aspx
 [3]: http://www.brentozar.com/archive/2015/05/announcing-live-query-execution-plans/
 [4]: http://sqlblog.com/blogs/jorg_klein/archive/2015/05/22/bi-on-your-terms-with-sql-server-2016.aspx
 [5]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2015/05/polybase_jre.jpg