---
title: Quest LiteSpeed update 5.0.2.1030 Released
author: Ted Krueger (onpnt)
type: post
date: 2008-12-17T11:10:46+00:00
ID: 254
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/quest-litespeed-update-5-0-2-1030-releas/
views:
  - 4683
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server Admin

---
For all you LiteSpeed users out there Quest released version 5.0.2 late yesterday. There are key features and problems addressed in this release. One of the largest is the recent release had problems with log shipping on standard edition.

Another key note: “Advanced Compression was removed from LiteSpeed 5.0.2 to mitigate any possible, future Microsoft support issues with the proprietary format. Any jobs containing the parameter will continue to run successfully, but the engine will ignore the parameter. Any existing backups created with the Advanced Compression option will continue to restore successfully in 5.0.2.”

features.
  
* Upgrade LiteSpeed no longer registers maintenance plan DLLs in GAC when upgrading to the latest version.
  
* Job Manager Resizing the console inside Job Manager no longer causes errors.
  
* Restores of striped backups no longer hang.
  
* xp\_restore\_headeronly no longer causes memory leaks.
  
* Log shipping supports SQL Server 2005 Standard Edition. 

If you are a LiteSpeed customer I recommend going out and start your testing with the new release.