---
title: SQL Server DBA Tip 5 – Server Considerations – Installing Features (SSIS, SSRS, Engine, SSAS)
author: Ted Krueger (onpnt)
type: post
date: 2011-05-02T10:26:00+00:00
ID: 1130
excerpt: SQL Server performs best when paired with hardware and an OS configured only for the core functionality of the SQL Server Engine.  Installing other features along with the engine can degrade performance and the engine's ability to process transactions. &hellip;
url: /index.php/datamgmt/dbadmin/sql-server-dba-tip-4/
views:
  - 4855
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
SQL Server performs best when paired with hardware and an OS configured only for the core functionality of the SQL Server Engine.  Installing other features along with the engine can degrade performance and the engine's ability to process transactions.  With this knowledge, why would anyone ever want to install one of the SQL Server features with the core engine?  This usually comes down to cost.

**Cost considerations when installing features of SQL Server**

The major cost of moving features like SSIS, SSRS and SSAS off the physical server in which the engine is installed is licensing.  These SQL Server features are essentially free while contained in the licensed area of the physical server the engine resides on.  Once any or all of these features are moved to a different physical server, another license is required. 

Licensing can be daunting, but also has many configurations to help with lowering the cost.  There is often the thought that a CPU license (or unlimited connection license) is the requirement.  When considering something like SSIS licensing, this may not be true.  If one connection is using SSIS to perform processing, the possibility of only one CAL or Device exists.  Take these into account when determining licensing costs and it is highly recommended you contact Microsoft before making a purchase to review the results.  In the resources section a download link to licenses has been listed.  Refer to it for much more in-depth details on SQL Server licensing.

**Suggested Feature Topology**

With licensing and hardware costs aside, the recommendation is to always separate the feature installations from the primary engine being used by the business.  Troubleshooting and optimizing these installations as single entities is much simpler.  This allows for quicker problem resolution and more proactive performance tuning, especially with limited DBA resources.

<div class="image_block">
  <a href="/media/blogs/DataMgmt/-45.png?mtime=1303501614"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/-45.png?mtime=1303501614" alt="" width="476" height="256" /></a>
</div>

The physical setup above, with fully licensed models, also allows for workloads to be distributed <span style="text-decoration: line-through;">set</span> across the environment.  This can prevent unwanted waits on the business's primary database server(s).  Use time and internal features such as mirroring and database snapshots to move data between the servers.  This movement allows for optimal performance for the customer facing requests.

**Resources**

[SQL Server 2008 R2 Overview of Licensing][1]

[Download SQL Server 2008 R2 Quick Reference Guide][2]

 [1]: http://www.microsoft.com/sqlserver/2008/en/us/pricing.aspx
 [2]: http://download.microsoft.com/download/2/7/0/270B6380-8B38-4268-8AD0-F480A139AB19/SQL2008R2_LicensingQuickReference-updated.pdf