---
title: Using STOPAT in the RESTORE DATABASE Statement
author: Jes Borland
type: post
date: 2011-04-04T13:03:00+00:00
ID: 1097
excerpt: One of the most important tasks a DBA should know how to perform, and should test regularly, is a database restore. It is absolutely necessary to back up your data so you have it if a disaster strikes, but it is equally important that you know how to retrieve that data when needed.
url: /index.php/datamgmt/dbprogramming/using-stopat-in-the-restore/
views:
  - 14650
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
One of the most important tasks a DBA should know how to perform, and should test regularly, is a database restore. It is absolutely necessary to back up your data so you have it if a disaster strikes, but it is equally important that you know how to retrieve that data when needed. 

The basic RESTORE syntax looks like: 

```sql
RESTORE DATABASE [Name] FROM [Device] WITH [Options]…
```

That restores an entire database to the last full backup. You can restore specific files, filegroups, and pages. You can also restore transaction log backups, if you have those for the database. 

I had become very familiar with database backups and restores. Most of my experience with restores was to restore either a full backup to a different environment, or restore a full backup and as many non-corrupt transaction logs as possible. 

One day, however, a developer came to me for help. An application change had been made which entered unwanted information in the database. We had a full backup from the previous night, and transaction logs through the current moment. The last full transaction log backup was from 11:00 AM. The user wanted QA restored to 2:00 PM, when this change was made. It was 4:00 PM. 

I was going to tell him it couldn’t be done, that I could only restore to 11:00 AM. I was quickly corrected, however, and I learned something new! Using STOPAT, you can restore only to a specific point in time in a database or log backup. 

[<img src="http://farm6.static.flickr.com/5143/5562927331_d499103e16.jpg" width="250" height="165" alt="SQL Saturday 2011 Raffle 110" />][1]
  
Yes, I was that excited! 

**Restoring to a Point in Time** 

I have a table with ID, DateAdded, and TimeAdded fields. 

<div class="image_block">
  <a href="/wp-content/uploads/users/grrlgeek/StopAt1.JPG?mtime=1301928592"><img alt="" src="/wp-content/uploads/users/grrlgeek/StopAt1.JPG?mtime=1301928592" width="256" height="126" /></a>
</div>

I take a full backup of my database at 11:23 AM. 

<div class="image_block">
  <a href="/wp-content/uploads/users/grrlgeek/StopAt2.jpg?mtime=1301928593"><img alt="" src="/wp-content/uploads/users/grrlgeek/StopAt2.jpg?mtime=1301928593" width="453" height="60" /></a>
</div>

I insert two more rows into the table. 

<div class="image_block">
  <a href="/wp-content/uploads/users/grrlgeek/StopAt3.jpg?mtime=1301928593"><img alt="" src="/wp-content/uploads/users/grrlgeek/StopAt3.jpg?mtime=1301928593" width="238" height="168" /></a>
</div>

I take a transaction log backup at 11:28 AM. 

<div class="image_block">
  <a href="/wp-content/uploads/users/grrlgeek/StopAt4.jpg?mtime=1301928593"><img alt="" src="/wp-content/uploads/users/grrlgeek/StopAt4.jpg?mtime=1301928593" width="460" height="56" /></a>
</div>

I delete a record. 

<div class="image_block">
  <a href="/wp-content/uploads/users/grrlgeek/StopAt5.jpg?mtime=1301928593"><img alt="" src="/wp-content/uploads/users/grrlgeek/StopAt5.jpg?mtime=1301928593" width="243" height="144" /></a>
</div>

I take another transaction log backup at 11:38 AM. 

<div class="image_block">
  <a href="/wp-content/uploads/users/grrlgeek/StopAt6.jpg?mtime=1301928593"><img alt="" src="/wp-content/uploads/users/grrlgeek/StopAt6.jpg?mtime=1301928593" width="455" height="56" /></a>
</div>

I insert two more records. 

<div class="image_block">
  <a href="/wp-content/uploads/users/grrlgeek/StopAt7.jpg?mtime=1301928593"><img alt="" src="/wp-content/uploads/users/grrlgeek/StopAt7.jpg?mtime=1301928593" width="250" height="187" /></a>
</div>

I take another transaction log backup at 11:49 AM. 

<div class="image_block">
  <a href="/wp-content/uploads/users/grrlgeek/StopAt8.jpg?mtime=1301928593"><img alt="" src="/wp-content/uploads/users/grrlgeek/StopAt8.jpg?mtime=1301928593" width="457" height="57" /></a>
</div>

I insert another record. 

<div class="image_block">
  <a href="/wp-content/uploads/users/grrlgeek/StopAt9.jpg?mtime=1301928593"><img alt="" src="/wp-content/uploads/users/grrlgeek/StopAt9.jpg?mtime=1301928593" width="239" height="201" /></a>
</div>

I realize that an application change made at 11:40 was not needed. I want to restore the database to 11:40:00, essentially leaving record 7 but removing records 8 and 9. My transaction log backups were taken at 11:38 and 11:49.
  
Here are the RESTORE statements I would use. Note the last one, which includes the command 

```sql
WITH STOPAT = 'Apr 3, 2011 11:40 AM'
```

This tells SQL Server to restore only to that point in time. 

```sql
RESTORE DATABASE AdventureWorks
   FROM DISK = N'C:Program FilesMicrosoft SQL ServerMSSQL10_50.MSSQLSERVERMSSQLBackupAdventureWorks.bak' WITH NORECOVERY;

RESTORE LOG AdventureWorks
   FROM DISK = N'C:Program FilesMicrosoft SQL ServerMSSQL10_50.MSSQLSERVERMSSQLBackupAdventureWorks.trn' WITH NORECOVERY; 
   
RESTORE LOG AdventureWorks 
   FROM DISK = N'C:Program FilesMicrosoft SQL ServerMSSQL10_50.MSSQLSERVERMSSQLBackupAdventureWorks2.trn' WITH NORECOVERY;

RESTORE LOG AdventureWorks 
   FROM DISK = N'C:Program FilesMicrosoft SQL ServerMSSQL10_50.MSSQLSERVERMSSQLBackupAdventureWorks3.trn' WITH STOPAT = 'Apr 3, 2011 11:40 AM', RECOVERY;
```

When I select rows from my table, I can see that rows 1, 2, 3, 5 and 6 are included. These were part of the transaction log backup taken at 11:38 AM. From the transaction log backup taken at 11:49, only transactions entered before 11:40 AM are restored – row 7. 

<div class="image_block">
  <a href="/wp-content/uploads/users/grrlgeek/StopAt10.jpg?mtime=1301928593"><img alt="" src="/wp-content/uploads/users/grrlgeek/StopAt10.jpg?mtime=1301928593" width="241" height="172" /></a>
</div>

**RESTORE: Learn, Understand, Use** 

The RESTORE command is a necessary tool in your database administration toolbox. It is up to you to learn it, understand it, and (most importantly) use it! Study the different options available, and make sure you know both how and when to use them.

 [1]: http://www.flickr.com/photos/m-i-k-e/5562927331/ "SQL Saturday 2011 Raffle 110 by Michael Kappel, on Flickr"