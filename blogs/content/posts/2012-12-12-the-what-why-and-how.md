---
title: \"The What, Why, and How of Filegroups\" – I-380 PASS Materials
author: Jes Borland
type: post
date: 2012-12-12T19:42:00+00:00
ID: 1852
excerpt: |
  Last night I had the pleasure of presenting \"The What, Why, and How of Filegroups\" for the East Iowa I-380 SQL Server user group. Thanks for having me!
  My presentation materials are available here.
  During the presentation, I show the DBCC CHECKFILEGRO&hellip;
url: /index.php/datamgmt/dbprogramming/the-what-why-and-how/
views:
  - 4079
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
Last night I had the pleasure of presenting "The What, Why, and How of Filegroups" for the [East Iowa I-380 SQL Server user group][1]. Thanks for having me!

My presentation materials are available [here][2].

During the presentation, I show the DBCC CHECKFILEGROUP command. An attendee asked if running this command instead of a full DBCC CHECKDB would reduce the size of the snapshot created. I asked my good friend Erin Stellato ([blog][3] | [twitter][4]), and she gave me this excellent information:

<p style="padding-left: 30px;">
  <span style="font-size: small;">"<span style="color: #222222; line-height: normal;">When you run any CHECK, it has to create a consistent view of the database, hence the snapshot.ï¿½ Even if you're just checking a filegroup (or one table), it needs the entire database to be transactionally consistent, not just a table or filegroup, so the snapshot of the database gets created.</span><br style="color: #222222; font-family: arial, sans-serif; line-height: normal;" /><br style="color: #222222; font-family: arial, sans-serif; line-height: normal;" /><span style="color: #222222; line-height: normal;">Now, a couple things to remember about the snapshot.ï¿½ DBCC creates an NTFS alternate stream for each file in a database.ï¿½ If you have 4 filegroups with 2 files each, you get 8 alternate streams, one for each file.ï¿½ But of course, the snapshot is empty initially, it starts to grow as changes are made to pages while DBCC runs.ï¿½ So...the size of the snapshot is ultimately dependent on how many pages are being changed while DBCC is running, not what kind of DBCC you're running." </span></span>
</p>

<span style="font-size: small;"><span style="color: #222222; line-height: normal;">Thanks for the info, Erin! </span></span>

 [1]: http://380pass.org/
 [2]: /media/users/grrlgeek/East Iowa 201212.zip?mtime=1355347802
 [3]: http://erinstellato.com/
 [4]: https://twitter.com/erinstellato