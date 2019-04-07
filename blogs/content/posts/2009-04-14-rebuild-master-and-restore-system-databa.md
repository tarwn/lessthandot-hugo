---
title: Rebuild master and restore system databases from complete disk failure
author: Ted Krueger (onpnt)
type: post
date: 2009-04-14T16:43:54+00:00
url: /index.php/datamgmt/dbadmin/rebuild-master-and-restore-system-databa/
views:
  - 20147
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server Admin

---
I just had the pleasure of going through this again on my machine so I figured I would put it up there for people that have yet to gain the experience of a complete SQL Server failure from system database corruption (suspect), disk failure and a few others. 

I’m going to break my favorite installation of SQL Server 2005 so you can see how to recover it. 

First to show the status of master we run

<pre>SELECT DATABASEPROPERTYEX('master','Status')</pre>

So master is online. Not for long! Easiest way I can break this thing is to stop SQL Server Services and delete master.mdf. Essentially this can replicate a complete loss of disk and the system databases. I’m hoping you configured these data files on drives that only consist of them. If you have binary there then you’ll be up awhile installing everything and hope you remember your configuration changes. Before you do anything more with this blog, backup the system databases. That includes master, msdb and model. Model is not so important but you should be backing these up on any instance. So if you are not, do it! Trust me; you don’t want to attempt a recovery without them. You will lose plenty of sleep and possibly your job. I use model for default database settings. This makes creating new databases a bit faster when you know what you standard database settings are. 

Right click your instance and hit stop. Navigate to the data directory. Mine is
  
C:Program FilesMicrosoft SQL ServerMSSQL.1MSSQLData

Delete master.mdf (don’t be scared….do it. And don’t be sissy and cut/paste ;))

Go back and try to start SQL Server. BOOM!!!

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//sysdb_1.gif" alt="" title="" />
</div>

Now for the fun of recovering it!

If you are running SQL Server 2000 this is actually easier for you (sometimes). The utility rebuild is sitting in your BINN directory and will rebuild the master.mdf for you. Then you can single-user connect and restore the master.bak to the rebuilt one. Then restore your other system databases.

Rebuildm.exe has been discontinued though in 2005 and up. Wish they only made a better rebuild.exe and not discontinued it but we can work around it. Basically this utility has been rolled into the main setup.exe. Luckily we still do have the same functionality but from the setup media. It’s actually just as easy as rebuild.exe but often mid-level DBAs will run to throwing the setup media in and reinstalling everything. Then you really have to rebuild it all. Not fun!
  
So hopefully you can find the media for SQL Server. It also has to be the same edition you originally installed. Meaning you cannot use Enterprise media to recover Standard edition. I wouldn’t try even if it worked or works that way. Throw it in the drive and start command prompt.

The command we need is
  
<code class="codespan"><br />
start /wait E:ServersSetupsetup.exe /qn INSTANCENAME=MSSQLSERVER REINSTALL=SQL_Engine REBUILDDATABASE=1 SAPWD=really%strong_p@$$w0rD<br />
</code>
  
I won’t go into this much more than MSSQLSERVER is for default instances, the /qn is to not show me setup boxes and status, REBUILDDATABASES switch is the key and the SAPWD is what it sounds like, you need to give a new sa password. DON’T FORGET THIS PASSWORD!!! Although you can recover from it if you have BUILTIN/ADMINS enabled after you restore msdb.

As much as you may want to see pop ups and setup status windows, you cannot when rebuilding components. The installations must be unattended. Basically throwing the /qn switch in there to suppress them. You can use the /qb command which will show you status of the setup process. I will for this just to show you how it runs.

You can find a complete listing of setup options and installations command here
  
http://msdn.microsoft.com/en-us/library/ms144259(SQL.90).aspx
  
Another handy link
  
http://msdn.microsoft.com/en-us/library/ms144259(SQL.90).aspx#rebuilddatabase

So run the command.

After executing the command you will get the typical setup window checking your configuration and setup files.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//sysdb_2.gif" alt="" title="" />
</div>

At some point you’ll get a few warnings. Click Yes, Accept to all of them. Primarily say yes to the warning that you will re-install the system databases.

After that it will show you the progress as it goes along.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//sysdb_3.gif" alt="" title="" />
</div>

You should see the system databases being populated in the Data directory now (or your data location). \*sigh\* of relief eh 🙂
  
Once done the setup status window closes itself and you are ready to get back to your current build and restore your original system databases.

Import note. You need to apply service packs now. It shouldn’t really be that difficult to track down what build you were at if you know your database servers. Basically the build must be at the same level your backup is at.
  
http://support.microsoft.com/kb/264474
  
This is actually kind of true. It will work without going up, but not safe!!!

After you are up on the build level, start SQL Server in single user mode now. I like command prompt just by using NET START &#8220;MSSQLSERVER&#8221; /m just for simple use of it.

Now if you use SSMS to restore your master there is a catch. When you use SSMS and the restore wizard it will restore master fine. Of course it will not be in the drop down but if you manually type is, then it will restore. However when master is restored it will issue a stop to the services so the GUI will die on you and error out. The restore was successful though. Net start SQL Server again and check it out. Database listing should be back and any other changes you made. Now restore model and msdb. Just remember, msdb holds you security settings so when you restore it, that new password we gave sa will revert back to the old. Don’t forget to validate orphaned logins, update statistics and all that fun rebuild tasks you should do. Of course tell them you saved the day first.

Hope you don’t ever have to do this, but please practice it because you don’t want to try and figure it all out when your disk is gone and you’re sitting there holding you server and crying.