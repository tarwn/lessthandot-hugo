---
title: SQL Server 2016 CTP 2.1 has been released
author: Koen Verbeeck
type: post
date: 2015-06-29T12:59:52+00:00
ID: 3432
url: /index.php/datamgmt/dbprogramming/mssqlserver/sql-server-2016-ctp-2-1-has-been-released/
views:
  - 25072
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server
tags:
  - sql 2016
  - sql server
  - syndicated

---
Microsoft is all about rapid release cycles nowadays. We already experienced this with Power BI, where monthly new features and improvements have been added.
  
Now SQL Server appears to get the same treatment: [SQL Server 2016 CTP 2.1 has just been released][1]. There is nothing groundbreaking in this release – which can be considered as an upgrade of CTP2 – but there are some additional functionalities and improvements in the database engine.

If you have already installed CTP2, you can just upgrade it to CTP2.1 following the usual upgrade procedure. Just spin-up the SQL Server set-up, go to Installation and choose _Upgrade from a previous version of SQL Server_. Hit Next a few times, choose which instance and which features you want to upgrade and you're all done.

Before:

[<img class="alignnone size-full wp-image-3433" src="/wp-content/uploads/2015/06/version_1.png" alt="version_1" width="350" height="141" srcset="/wp-content/uploads/2015/06/version_1.png 350w, /wp-content/uploads/2015/06/version_1-300x120.png 300w" sizes="(max-width: 350px) 100vw, 350px" />][2]

After:

[<img class="alignnone size-full wp-image-3434" src="/wp-content/uploads/2015/06/version_2.png" alt="version_2" width="317" height="145" srcset="/wp-content/uploads/2015/06/version_2.png 317w, /wp-content/uploads/2015/06/version_2-300x137.png 300w" sizes="(max-width: 317px) 100vw, 317px" />][3]

One of the new features for example is the ability to use computed columns in a temporal table. Exciting! (OK, I'm just using this as an example that I actually upgraded my system)

[<img class="alignnone size-full wp-image-3437" src="/wp-content/uploads/2015/06/temporal_before.png" alt="temporal_before" width="973" height="317" srcset="/wp-content/uploads/2015/06/temporal_before.png 973w, /wp-content/uploads/2015/06/temporal_before-300x97.png 300w" sizes="(max-width: 973px) 100vw, 973px" />][4]

After the upgrade:

[<img class="alignnone size-full wp-image-3441" src="/wp-content/uploads/2015/06/temporal_after-e1435582654552.png" alt="temporal_after" width="640" height="231" srcset="/wp-content/uploads/2015/06/temporal_after-e1435582654552.png 640w, /wp-content/uploads/2015/06/temporal_after-e1435582654552-300x108.png 300w" sizes="(max-width: 640px) 100vw, 640px" />][5]

&nbsp;

Anyway, I hope we can see more incremental releases of SQL 2016 soon and dare I say with more exciting Business Intelligence features.

 [1]: http://blogs.technet.com/b/dataplatforminsider/archive/2015/06/24/sql-server-2016-community-technology-preview-2-1-is-available.aspx
 [2]: /wp-content/uploads/2015/06/version_1.png
 [3]: /wp-content/uploads/2015/06/version_2.png
 [4]: /wp-content/uploads/2015/06/temporal_before.png
 [5]: /wp-content/uploads/2015/06/temporal_after-e1435582654552.png