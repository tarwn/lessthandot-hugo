---
title: Master Data Services stops working after an upgrade
author: Koen Verbeeck
type: post
date: 2013-11-27T11:57:00+00:00
ID: 2200
excerpt: 'Recently some SQL Server instances at a client were upgraded to the latest cumulative update of SQL Server 2012 service pack 1. The reason for this update was the added functionality to create Power View reports on top of Analysis Services Multidimensio&hellip;'
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/master-data-services-stops-working/
views:
  - 31453
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - Master Data Services (MDS)
  - Microsoft SQL Server Admin
tags:
  - cumulative update
  - error
  - master data services
  - mds
  - sql server
  - syndicated
  - version

---
<p style="text-align: justify;">
  Recently some SQL Server instances at a client were upgraded to the latest cumulative update of SQL Server 2012 service pack 1. The reason for this update was the added functionality to create Power View reports on top of Analysis Services Multidimensional. However, one of the instances also has Master Data Services (MDS) installed and after the update we couldn’t connect to the Master Data Manage anymore using the browser. There were two separate issues that had to be solved.
</p>

<h3 style="text-align: justify;">
  Lost permissions
</h3>

<p style="text-align: justify;">
  Somehow some permissions were lost during the upgrade, leading to the following error screen when trying to connect:
</p>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/MDSStopsWorking/ErrorScreen1.jpg?mtime=1385540686"><img src="/wp-content/uploads/users/koenverbeeck/MDSStopsWorking/ErrorScreen1.jpg?mtime=1385540686" alt="" width="773" height="406" /></a>
</p>

<p style="text-align: justify;">
  For once, the error message is crystal clear: <em>Cannot read configuration file due to insufficient permissions</em>. It even details the location of the web.config file. All we have to do now is add the local account <strong>MDS_ServiceAccounts</strong> if it isn’t added yet and give this account read permission on the config file.
</p>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/MDSStopsWorking/webconfigperms.png?mtime=1385540749"><img src="/wp-content/uploads/users/koenverbeeck/MDSStopsWorking/webconfigperms.png?mtime=1385540749" alt="" width="292" height="354" /></a>
</p>

<p style="text-align: justify;">
  This should get rid of the internal error message.
</p>

<h3 style="text-align: justify;">
  MDS Database needs an upgrade
</h3>

<p style="text-align: justify;">
  Unfortunately our problems weren’t over, as we were now greeted with another error screen when trying to connect.
</p>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/MDSStopsWorking/ErrorScreen2.jpg?mtime=1385540686"><img src="/wp-content/uploads/users/koenverbeeck/MDSStopsWorking/ErrorScreen2.jpg?mtime=1385540686" alt="" width="646" height="240" /></a>
</p>

<p style="text-align: justify;">
  MDS now complains the database version (meaning SQL Server) has a more recent version than the client (which is MDS itself). Let’s take a look at the MDS Server Configuration Manager to see what is going on. Make sure you open the Configuration Manager using admin privileges. We can immediately see that something is wrong at the welcome screen:
</p>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/MDSStopsWorking/configscreen_1.png?mtime=1385540686"><img src="/wp-content/uploads/users/koenverbeeck/MDSStopsWorking/configscreen_1.png?mtime=1385540686" alt="" width="405" height="304" /></a>
</p>

<p style="text-align: justify;">
  It looks like no SQL Server database has been configured, while this was the case before the upgrade. Let’s go to the database configuration tab.
</p>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/MDSStopsWorking/configscreen_2.png?mtime=1385540686"><img src="/wp-content/uploads/users/koenverbeeck/MDSStopsWorking/configscreen_2.png?mtime=1385540686" alt="" width="420" height="263" /></a>
</p>

<p style="text-align: justify;">
  Hmm, this looks pretty empty. Click on <em>Select Database…</em> and configure your MDS database in the prompt.
</p>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/MDSStopsWorking/configscreen_3.png?mtime=1385540686"><img src="/wp-content/uploads/users/koenverbeeck/MDSStopsWorking/configscreen_3.png?mtime=1385540686" alt="" width="375" height="294" /></a>
</p>

<p style="text-align: justify;">
  The result already looks much better, except the error in bright red of course.
</p>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/MDSStopsWorking/configscreen_4.png?mtime=1385540686"><img src="/wp-content/uploads/users/koenverbeeck/MDSStopsWorking/configscreen_4.png?mtime=1385540686" alt="" width="593" height="156" /></a>
</p>

<p style="text-align: justify;">
  This error says the database needs to be upgraded. Right next to it is a button called <em>Upgrade Database</em> so it seems obvious we’ll click that one. This will start a wizard which will guide you through the upgrade process. Hit <em>Next</em> until the upgrading begins. The wizard will fire off some scripts against the database to get it to consistent version.
</p>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/MDSStopsWorking/configscreen_7.png?mtime=1385540686"><img src="/wp-content/uploads/users/koenverbeeck/MDSStopsWorking/configscreen_7.png?mtime=1385540686" alt="" width="476" height="342" /></a>
</p>

<p style="text-align: justify;">
  Once the wizard has finished, everything looks OK in the configuration screen:
</p>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/MDSStopsWorking/configscreen_8.png?mtime=1385540686"><img src="/wp-content/uploads/users/koenverbeeck/MDSStopsWorking/configscreen_8.png?mtime=1385540686" alt="" width="410" height="282" /></a>
</p>

<p style="text-align: justify;">
  Now we can connect back to the Master Data Manager using the browser or Excel (if you have the plug-in installed).
</p>