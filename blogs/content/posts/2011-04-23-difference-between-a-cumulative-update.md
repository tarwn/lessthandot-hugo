---
title: Difference between a Cumulative Update and Service Pack for SQL Server
author: Ted Krueger (onpnt)
type: post
date: 2011-04-23T11:12:00+00:00
ID: 1136
excerpt: |
  I read a #SQLHELP tweet that stated it would have been nice if the SQL Server team would have waited to announce SQL Server 2008 SP1 CTP release for testing before releasing SQL Server 2008 SP1 Cumulative Update 7.
  To me, the tweet didn’t make much sen&hellip;
url: /index.php/datamgmt/datadesign/difference-between-a-cumulative-update/
views:
  - 20084
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
I read a [#SQLHELP tweet][1] that stated it would have been nice if the SQL Server team would have waited to announce [SQL Server 2008 SP1 CTP release][2] for testing before releasing [SQL Server 2008 SP1 Cumulative Update 7][3].

To me, the tweet didn’t make much sense given the differences between a Service Pack and a Cumulative Update.  A Service Pack does include all previous hotfixes that were included in Cumulative Updates.  However, the difference in a Service Pack is they include actual changes or new enhancing features.  Cumulative Updates are primarily released for hotfix purposes.

Aside from the fact that the R2 SP1 release is a CTP release and in no way should go into a production SQL Server and CU7 was a production release.  These two updates have many differences and reasons why you would or wouldn’t apply them should be clear.  So releasing an SP at any given time should not truly revolve around when a CU is released.  The other catch here is CU7 is for SQL Server 2008 SP1, not SQL Server 2008 R2.  Now that is Microsoft’s issue with the release of R2 being confusing and just annoying.  Many agree that this full version release was poorly done but it is there nonetheless.  Just because it doesn’t say, SQL Server 2009 or such, it needs to be treated as a full version release.

I could see it being a problem is a production release of a Service Pack was done days after a Cumulative Update for the same version.  That would flare some anger if the timing was just right and DBAs had tested and applied the CU already.  To my recollection, that isn’t common or happened much. 

If you are interested in seeing what enhancements are in SQL Server 2008 SP1 CTP, check Denis Gobo’s post, “[SQL Server 2008 R2 SP1 CTP Available for download][4]”.

Remember, not all installations have the need for applying all Cumulative Updates.  In fact, if particular problems that many hotfixes repair are not present on your installations, waiting for the Service Pack is a sound strategy.  That does not mean that you should not monitor the CU releases and what they include for hotfixes.  If a CU has a hotfix for a critical area of SQL Server that is in a way, sitting dormant on all SQL Server installations, you should consider applying it. 

Always test your SP and CU installations on development and test environments.  Applying any update should be a stressed process and not taken lightly.

 [1]: http://bit.ly/gZTEIh
 [2]: http://blogs.technet.com/b/dataplatforminsider/archive/2011/04/22/sql-server-2008-r2-sp1-ctp-now-available-for-testing.aspx
 [3]: http://support.microsoft.com/kb/979065
 [4]: /index.php/DataMgmt/DBProgramming/MSSQLServer/sql-server-2008-r2-sp1