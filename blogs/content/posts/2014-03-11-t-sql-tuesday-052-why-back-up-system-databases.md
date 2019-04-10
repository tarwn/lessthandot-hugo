---
title: 'T-SQL Tuesday #052 – Why Back Up System Databases?'
author: Jes Borland
type: post
date: 2014-03-11T21:29:54+00:00
ID: 2498
url: /index.php/uncategorized/t-sql-tuesday-052-why-back-up-system-databases/
views:
  - 11876
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server Admin
  - Uncategorized

---
[<img class="alignleft size-full wp-image-2241" alt="TSQL2sday" src="/wp-content/uploads/2014/01/TSQL2sday.png" width="133" height="134" />][1]It's T-SQL Tuesday! This month, <a href="http://michaeljswart.com/2014/03/argue_against_a_popular_opinion/" target="_blank">Michael Swart has challenged us to argue against a popularly-held opinion about SQL Server</a>. “Arguing Against Popular Opinion” sounds like the title of my autobiography.

You’ve been told time and again to back up your SQL Server system databases – master, msdb, and model. I’m here to tell you it’s not that important, especially if you have a large number of SQL Server instances. The information in those databases should be documented, scripted, and available in other locations, not just contained here.

**TL;DR:** Make sure all information contained in the system databases is documented and/or scripted in another location. Before you are forced to restore system databases in an emergency, practice doing it in a test system _just because_ – there are some important steps to take and lessons to be leared.

### master

<a href="http://technet.microsoft.com/en-us/library/ms187837.aspx" target="_blank">Master</a> is a very important database for every instance of SQL Server. It contains such valuable information as configuration options, the names and locations of all database files, logins, and endpoints.

The information contained here should be documented in other places. All your logins should be Windows groups, if possible. Very, very few logins should be granted sa rights. Your system configuration options should be in your instance documentation. If you have mirroring set up, this too should be documented. The information contained in master should be documented and scripted somewhere.

Given the choice between restoring a copy of the master database on a new server and creating a new master database and applying settings, I would generally choose to create a new database, then apply my settings and configuration changes.

### msdb

<a href="http://technet.microsoft.com/en-us/library/ms187112.aspx" target="_blank">Msdb </a>holds information about backup locations and history, Database Mail, SQL Server Agent, Policy Based Management, Service Broker, and more. This is also really important information, and it should never be left to one location.

The information you’re storing here should be scripted out and in source control. Database Mail should have a standard configuration across servers. You should be able to recreate your Jobs by running a saved script. Reporting Services subscriptions are stored in the Report Server database, which should be backed up on a regular basis (along with the SSRS Encryption Key). None of the information in msdb should be solely in msdb.

### model

<a href="http://technet.microsoft.com/en-us/library/ms186388.aspx" target="_blank">Model </a>holds the settings that will be applied to new databases if all settings aren’t explicitly declared upon creation – for example, the recovery model, file locations, and file growth settings. You don’t need to back this up. Why?

When you or your developers are creating a database, you should have the relevant properties defined. There should be company-wide standards as to what should be used. If you’re working with a vendor, make sure you know what settings their database will use. And if someone forgets an important setting? You should be defining the settings for the model database in your basic SQL Server setup/deployment scripts, and they shouldn’t change from instance to instance.

### When Disaster Strikes

You need to consider the process of [restoring these system databases][2]. Have you tried it? Did you know you can’t just issue a RESTORE command and continue on with your day? What if you have a clustered SQL Server instance? In my experience, I find it is easier to script out or document all information contained in the system databases, and if there is a server failure, build a fresh new server and start from scratch.

This is a situation you should test at least once – before you need to do it in an emergency. In your sandbox environment, take backups of those databases and try to restore them. Run through the steps in the article listed above. How long does it take? Do you have all the information you need? Is it easier to stand up a new server and apply settings and scripts?

Like many things in SQL Server, there is more than one way to approach this problem. Make sure you've looked at all of them, so you know what is appropriate for your environment!

 [1]: http://michaeljswart.com/2014/03/argue_against_a_popular_opinion/
 [2]: http://technet.microsoft.com/en-us/library/dd207003.aspx