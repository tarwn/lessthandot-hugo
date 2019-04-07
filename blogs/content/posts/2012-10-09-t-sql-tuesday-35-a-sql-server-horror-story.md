---
title: 'T-SQL Tuesday #35: Soylent Green SQL Server'
author: Jes Borland
type: post
date: 2012-10-09T17:43:00+00:00
ID: 1749
excerpt: |
  It’s October. That means fall, colorful leaves on the trees, soups simmering on the stove, and…horror movies. I love a good horror flick.
   
  
  http://www.flickr.com/photos/kaptainkobold/257491210/lightbox/
  It’s also another month of T-SQL Tuesday, edi&hellip;
url: /index.php/datamgmt/dbadmin/t-sql-tuesday-35-a-sql-server-horror-story/
views:
  - 8482
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server Admin

---
It’s October. That means fall, colorful leaves on the trees, soups simmering on the stove, and…horror movies. I love a good horror flick.

 

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/lego%20bride%20of%20frankenstein.jpg?mtime=1349811531" alt="" />
</p>

<address style="text-align: center;">
  <a href="http://www.flickr.com/photos/kaptainkobold/257491210/lightbox/">http://www.flickr.com/photos/kaptainkobold/257491210/lightbox/</a>
</address>

It’s also another month of T-SQL Tuesday, edition #35. Our host, Nick Haslam, asks, “What is your most horrifying discovery from your work with SQL Server?”

<p style="text-align: center;">
  <a href="http://blog.nhaslam.com/2012/10/04/t-sql-tuesday-35-soylent-green-tsql2sday/"><img src="http://nicksmsblog.files.wordpress.com/2012/10/20121003-200545.jpg?w=640" alt="" /></a>
</p>

**Once Upon a Time** 

Long, long ago, in a land far, far away, I was the very junior DBA at a small company. We had one main production server that hosted the databases for our financial and sales applications. I took great care of this server. I made sure backups ran every day. I tested restores (to a separate server, for reporting) regularly. DBCC CHECKDB, index maintenance, and statistics maintenance were run regularly. I had alerts set up in case jobs failed, and I could use Activity Monitor and some of my scripts to check for problems when I got the inevitable “the system is slow” calls.

Then, one dark and stormy night, a disaster happened.

The drive the data files were on failed. <cue creepy music>

I remained as calm as I could during My First DBA Disaster. Because we ran restores regularly, and occasionally they failed and I had to manually do them, I was able to walk through the steps quickly and without panic.

But things were not as they seemed. There was trouble. Users couldn’t connect. Applications couldn’t connect. Regularly scheduled jobs were running. Things didn’t seem…right. It was determined that while we were taking backups of the user databases, we weren’t backing up the system databases.

We didn’t have backups of master, model, or msdb. System configuration settings were lots. SQL Server Agent jobs were lost. It was a mess. We lost a day of business while re-configuring the server and database-level settings. We lost another day of backups and maintenance because Agent jobs had to be recreated and tested. We lost two days of work on projects and reports because we were busy mopping up the mess from that disaster.

**Frightened Into Backups** 

I learned the hard way that you need to back up both system and user databases. That was a change I implemented immediately. Every DBA should regularly review the backups on their servers to ensure that all the databases needed for recovery are backed up!

Do you want more guidance on which databases should be backed up, and what recovery model they should use? [Microsoft offers guidelines][1]!

OK, you can turn the lights on now!

 [1]: http://msdn.microsoft.com/en-us/library/ms190190.aspx