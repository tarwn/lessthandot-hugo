---
title: SQL Server 2008 R2 Setup starts and then disappears
author: Ted Krueger (onpnt)
type: post
date: 2011-06-06T00:50:00+00:00
ID: 1204
excerpt: 'I had an interesting problem tonight.  The symptoms were; after completely uninstalling SQL Server 2000, 2005, 2008 and 2008 R2 from a machine, running the setup to install a new instance would start fine but soon after running, disappear and seem to ha&hellip;'
url: /index.php/datamgmt/dbadmin/sql-server-2008-r2-setup/
views:
  - 15448
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server Admin

---
I had an interesting problem tonight. The symptoms were; after completely uninstalling SQL Server 2000, 2005, 2008 and 2008 R2 from a machine, running the setup to install a new instance would start fine but soon after running, disappear and seem to hang in the task manager.

This was for installing SQL Server 2008 R2 (all editions)

Previous installations uninstalled from ARP (Add/Remove Programs)

New installation started from the SQL Server 2008 R2 media and goes through the product key input and setup files steps. Then the actual installer is launched that allows the configuration of the new instance to be installed. This installer never actually comes up though. The Setup100.exe which sits behind the 2008 R2 installation stalls in the processes and never appears.

All the log files in the bootstrap folders were all clean and no errors appeared on the system anywhere (including the normal event viewer logs).

After some digging to see the state of the machine including the registry, old files and old files left form the original uninstallation, I removed the actual setup files installation from the ARP as well. In ARP, this is specifically listed as “Microsoft SQL Server 2008 Setup Support Files”. Removing the support files requires the original location of the SqlSupport.msi. This is the msi that seems to perform the installation of the support files.

Once uninstalled and rerunning the normal installation of SQL Server 2008 R2, the setup support files were installed again and the setup100.exe came up this time and allowed the configuration of the installation to proceed successfully.

If you are installing SQL Server 2008 R2 (or other version possibly) and the setup simply appears to disappear and never continue, trying to remove and reinstall the setup files may be a fix for you. It was for me.