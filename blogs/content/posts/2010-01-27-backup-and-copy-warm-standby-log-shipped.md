---
title: Backup and copy warm-standby (log shipped) databases in SQL Server
author: Ted Krueger (onpnt)
type: post
date: 2010-01-27T13:13:19+00:00
excerpt: 'Most DR solutions include log shipping strategies.  Log shipping (LS) is an extremely inexpensive solution for DR and also one I recommend.  There is little learning curve to individuals just coming into the administration career for setting LS up and m&hellip;'
url: /index.php/datamgmt/datadesign/backup-and-copy-warm-standby-log-shipped/
views:
  - 34372
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
Most DR solutions include log shipping strategies. Log shipping (LS) is an extremely inexpensive solution for DR and also one I recommend. There is little learning curve to individuals just coming into the administration career for setting LS up and maintaining the flow. 

One thing that will undoubtedly be asked of you at some point in time will be to make a backup of the warm-standby database for other purposes. Reasoning for this is commonly due to the geographical location of the standby database verses the online production environment. Often in order to move a backup that is larger in size, there will be several hours involved in the actual copy of the phsyical files and sometimes even days depending on the variables in place on the WAN. 

When you go to make a backup of a database in standby, you will be presented with the following
  

  
<span class="MT_red">The database &#8220;bah&#8221; is in warm-standby state (set by executing RESTORE WITH STANDBY) and cannot be backed up until the entire restore sequence is completed.</span>
  

  
In the state the database is in, you will not be able to use normal methods to get this task done. Depending on the restore rate you have for LS, you could also break the LS stream and in the worst case scenario, cause you to reinitialize the entire LS plan. I’d like to show you a quick and easy solution to that though. 

> Note: This method is the quickest way I have found. As always with my work, I am open to others knowing better and more efficient methods. There is a break point below in the copy. It is a good idea to disable you LS plan while you perform this to prevent failed restore job. 

Let’s setup a test database to play with. We’ll create, backup and restore to standby…
  


<pre>CREATE DATABASE bah
GO
BACKUP DATABASE bah TO DISK = 'C:bah.bak'
GO
RESTORE DATABASE [bah] FROM  
DISK = N'C:bah.bak' 
WITH  FILE = 1,  STANDBY = N'C:ROLLBACK_UNDO_bah.BAK',  
NOUNLOAD,  REPLACE,  STATS = 10
GO</pre>

We should now have, “bah (Standby /Read-Only) listed in our database tree.

The regular Copy Database with either detaching or SMO is not ideal for us at this point. We can however take this database offline, grab the mdf, ndf’s and ldf’s to bring to the other instance we want to restore them to. 

To do this we perform the following steps
  


<pre>USE MASTER
GO
ALTER DATABASE bah SET OFFLINE</pre>

Now go to the default folder where the mdf and other files are located and copy/paste them to a local drive. I mentioned local drive because we need to do this as quickly as possible. If there is enough free space locally, it will be the fastest copy transmission. Otherwise at this stage it is crucial to ensure you have disabled the LS plans so no unwanted jobs attempt to access the database while it is offline. 

Once you’ve copied the files off, run the following to bring the database back up

<pre>USE MASTER
GO
ALTER DATABASE bah SET ONLINE</pre>

We should see the database has come back up in standby mode and log shipping can proceed as normal.

Next, we only need to attach the files to a new instance given the following commands or using SSMS attach/detach wizard. 

> Note: You cannot bring these files up on the same instance. This is due to being required to leave the database &#8220;as is&#8221; and with the same file names. When the database was taken offline, it was in standby mode and will be the same when attached again during the initial loading. Once on the new instance, you can bring the database into any state you require and move it back to the other instance is required.

On the secondary instance do the following steps

<pre>CREATE DATABASE bah
ON 
( NAME = bah_data,FILENAME = 'c:restorebah.mdf')
LOG ON
( NAME = 'bah_log',
   FILENAME = 'c:restorebah_1.ldf' )
GO
ALTER DATABASE bah SET OFFLINE
GO
--Copy the bah files from our original standby database into the location you just created the 
--new database to.  in my case C:restore 
--make sure you replace the files and that the file names, both data and logs are identical to the original
ALTER DATABASE bah SET ONLINE
GO</pre>

Now we can see already in the tree of object explorer that the database online and read-write state. There is no need to issue a restore to recovery or anything. 

To verify, we can check the read only state in sys.databases

<pre>select is_read_only from sys.databases where [name] = 'bah'</pre>

This will result in 0 meaning the database is read-write 

We now have a backup of a warm-standby database that is used in a log shipping plan.