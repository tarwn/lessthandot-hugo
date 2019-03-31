---
title: SQL Server DBA Tip 6 – Server Security – Windows Authentication / SQL Authentication
author: Ted Krueger (onpnt)
type: post
date: 2011-05-03T10:29:00+00:00
excerpt: 'Defaults surround us in SQL Server.  This is both a good thing and a bad thing.  Part of the SQL Server installation process is the choice of mixed mode security.  Mixed mode in SQL Server means both SQL Authentication and Windows Authentication can be&hellip;'
url: /index.php/datamgmt/dbadmin/sql-server-dba-tip-mixed-mode/
views:
  - 7383
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
Defaults surround us in SQL Server.  This is both a good thing and a bad thing.  Part of the SQL Server installation process is the choice of mixed mode security.  Mixed mode in SQL Server means both SQL Authentication and Windows Authentication can be used.  By default, SQL Server has Windows Authentication selected.  Changing this default to mixed mode should be taken seriously.

**Mixed Mode vs. Windows Authentication**

When operating in Mixed Mode, both Windows Authentication and SQL Authentication can be utilized for connections to SQL Server.  Sometimes the choice is not up to us on using one or the other.  Applications that are built to use SQL Authentication force mixed mode over the [Microsoft recommended, Windows Authentication][1].  Windows Authentication is deemed more secure due to no password validation at the SQL Server level.  This is all handled in Windows and the principal token.  With SQL Authentication the passwords are validated and held on SQL Server.

**Why would I ever use SQL Authentication?**

Controlling security based on SQL Authentication isn’t a bad thing.  The loss of the domain and failures to login by means of Windows Authentication alone can push to enabling mixed mode.  One tip that should be noted with configuring mixed mode security: sa is a known system administrator account on SQL Server and is commonly part of malicious attacks.  Leaving this account enabled is not recommended.  To read more on enabling or disabling sa, [To SA or not to SA][2].  ****

**I didn’t enable mixed mode and need it now**

Luckily, if mixed mode was not selected and the need for SQL Authentication arises, it is a configuration change that can be performed without a major effect on SQL Server.  Restarting SQL Server is not required for the change and the user activities are not directly impacted at the time the change is made.

To change from Windows Authentication to Mixed Mode, use SSMS under the Server Properties and Security page.  Change Server Authentication as needed.

**Resources**

[Change Server Authentication Mode][3]

[Choosing an Authentication Mode][1]

[Security Considerations SQL Server][4]

[SQL Server 2005 (applicable for 2008) Best Practices][5]

 [1]: http://technet.microsoft.com/en-us/library/ms144284.aspx
 [2]: /index.php/DataMgmt/DBAdmin/to-sa-or-not-to-sa
 [3]: http://msdn.microsoft.com/en-us/library/ms188670.aspx
 [4]: http://msdn.microsoft.com/en-us/library/ms161948(v=sql.90).aspx
 [5]: http://download.microsoft.com/download/8/5/e/85eea4fa-b3bb-4426-97d0-7f7151b2011c/SQL2005SecBestPract.doc