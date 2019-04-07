---
title: Diagnostic Manager – Custom Alerts
author: Kevin Conan
type: post
date: 2012-04-13T09:43:00+00:00
ID: 1564
excerpt: 'One of the great things about Diagnostic Manager is the ability to create custom alerts.  This is something I believe needs a bit more work, but even in its current state, it is really useful.'
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/diagnostic-manager-custom-alerts/
views:
  - 14195
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server Admin
tags:
  - backups
  - diagnostic manager
  - idera
  - monitoring
  - sql

---
One of the great things about Diagnostic Manager is the ability to create custom alerts.  This is something I believe needs a bit more work, but even in its current state, it is really useful.

The example that we&#8217;ll walk through is something that would actually make a great built in alert.  We will create an alert based on the time of our oldest current database backup.

To start off, click on &#8220;Administration&#8221; in the bottom right hand corner:

<p style="text-align: left;">
  <a href="/media/users/kconan/DM - Administration Menu.JPG?mtime=1332527611"><img src="/wp-content/uploads/users/kconan/DM - Administration Menu.JPG?mtime=1332527611" alt="" width="302" height="203" /></a>
</p>

Next Click on Custom Counters (there are two spots that you can click on for it):

<div class="image_block">
  <div class="image_block" style="text-align: left;">
    <a href="/media/users/kconan/DM - Administration Menu2.JPG?mtime=1332527857"><img src="/wp-content/uploads/users/kconan/DM - Administration Menu2.JPG?mtime=1332527857" alt="" width="603" height="310" /></a>
  </div>
</div>

<div class="image_block" style="text-align: left;">
  Now in the menu ribbon, click on Add.
</div>

<div class="image_block" style="text-align: left;">
  <a href="/media/users/kconan/DM - Custom Counter Add.jpg?mtime=1332528329"><img src="/wp-content/uploads/users/kconan/DM - Custom Counter Add.jpg?mtime=1332528329" alt="" width="364" height="161" /></a>
</div>

On the first screen of the wizard, click on Next at the bottom.

<div class="image_block" style="text-align: left;">
  <a href="/media/users/kconan/DM - Custom Counter Wizard 1.JPG?mtime=1332528009"><img src="/wp-content/uploads/users/kconan/DM - Custom Counter Wizard 1.JPG?mtime=1332528009" alt="" width="633" height="564" /></a>
</div>

We are going to base our Custom Counter/Alert on a SQL Query, so choose the 3rd option: Custom SQL Script.

<div class="image_block" style="text-align: left;">
  <a href="/media/users/kconan/DM - Custom Counter Wizard 2.JPG?mtime=1332528329"><img src="/wp-content/uploads/users/kconan/DM - Custom Counter Wizard 2.JPG?mtime=1332528329" alt="" width="630" height="558" /></a>
</div>

On the next screen we add the SQL Query.

<div class="image_block" style="text-align: left;">
  <a href="/media/users/kconan/DM - Custom Counter Wizard 3.JPG?mtime=1332529354"><img src="/wp-content/uploads/users/kconan/DM - Custom Counter Wizard 3.JPG?mtime=1332529354" alt="" width="639" height="564" /></a>
</div>

<!-- codeblock="tsql" -->

    
    SELECT ROUND(MAX(DATEDIFF(MINUTE,LastBackUpTaken,GETDATE()))/60.0,2)  
    FROM  
    ( 
    SELECT sdb.Name AS DatabaseName
    ,COALESCE(MAX(bus.backup_finish_date),GETDATE()-100) AS LastBackUpTaken    
    FROM master..sysdatabases sdb    
    LEFT OUTER JOIN msdb.dbo.backupset bus    
    ON bus.database_name = sdb.name    
    WHERE sdb.Name NOT IN ('tempdb','model')   
    AND bus.type = 'L'   
    GROUP BY sdb.Name  
    ) 
    DBList;
    </pre>
    <p>*Note the code provided above is different from the screenshot. While proof reading this article, I realized that I forgot include the backup type in the query.</p>
    <p>On the next screen we want to leave the Customize Calculation Type set to "Use collected value".  We are doing this because we want to base the alert off the most recent value.  If we had chosen to set it to "Use per second value since last collection" then it would base the alert on the difference between the most recent two results of our query.</p>
    <p>On the same screen we also want to leave the Customize Scale Factor at 1.  We are doing this because we've already set our query to return the number of hours since the latest current backup was completed.</p>
    <div class="image_block" style="text-align: left;"><a href="/media/users/kconan/DM - Custom Counter Wizard 4.JPG?mtime=1332529893"><img src="/wp-content/uploads/users/kconan/DM - Custom Counter Wizard 4.JPG?mtime=1332529893" alt="" width="633" height="563" /></a></div>
    <p>If we click on the test button we can run the query on all or some of our monitored instances to ensure it works.  When you are finished testing, click on Done and then Next.</p>
    <div class="image_block" style="text-align: left;"><a href="/media/users/kconan/DM - Custom Counter Test.jpg?mtime=1332529893"><img src="/wp-content/uploads/users/kconan/DM - Custom Counter Test.jpg?mtime=1332529893" alt="" width="644" height="549" /></a></div>
    <p>You want to be sure to put a meaningful and descriptive name on the next screen.  This is the name that will appear in the alerts screen.</p>
    <p>For my case, I don't have many custom alerts so I keep keep the category set to Custom for all of them.</p>
    <p>I setthe description that you see in the screenshot below so that I include the code to find which databases need to be backed up is at my fingertips.</p>
    <div class="image_block" style="text-align: left;"><a href="/media/users/kconan/DM - Custom Counter Wizard 5.JPG?mtime=1332531299"><img src="/wp-content/uploads/users/kconan/DM - Custom Counter Wizard 5.JPG?mtime=1332531299" alt="" width="635" height="563" /></a></div>
    <p>On the next screen, we want to choose the option of "Higher values are worse than lower values" because a higher number means it's been longer since the last backup.</p>
    <p>I like to set the informational warning at 24 hours.  At that point it is not a problem, but something I want to be aware of.  The warning is set at 26 hours because it could just be that a backup is running long for some reason.  When we hit 28 hours that means that something is definately not right.</p>
    <p>This is the type of counter that I want on every instance no matter it is used for, so I check the bottom check box.</p>
    <div class="image_block" style="text-align: left;"><a href="/media/users/kconan/DM - Custom Counter Wizard 6.JPG?mtime=1332531299"><img src="/wp-content/uploads/users/kconan/DM - Custom Counter Wizard 6.JPG?mtime=1332531299" alt="" width="634" height="563" /></a></div>
    <p>The next screen just tells you that you have completed the wizard and warns you that you still need to link the custom counter to your monitored SQL Instances.</p>
    <div class="image_block" style="text-align: left;"><a href="/media/users/kconan/DM - Custom Counter Wizard 7.JPG?mtime=1332531299"><img src="/wp-content/uploads/users/kconan/DM - Custom Counter Wizard 7.JPG?mtime=1332531299" alt="" width="633" height="555" /></a></div>
    <p>On the next screen you can select whether or not you want to link this new customer counter your monitored SQL Instances right away.</p>
    <div class="image_block" style="text-align: left;"><a href="/media/users/kconan/DM - Custom Counter Wizard 8.JPG?mtime=1332531784"><img src="/wp-content/uploads/users/kconan/DM - Custom Counter Wizard 8.JPG?mtime=1332531784" alt="" width="624" height="141" /></a></div>
    <p>The other way to link the custom counter is to select the counter and choose link from the menu above it.</p>
    <div class="image_block" style="text-align: left;"><a href="/media/users/kconan/DM - Custom Counter List.jpg?mtime=1332531784"><img src="/wp-content/uploads/users/kconan/DM - Custom Counter List.jpg?mtime=1332531784" alt="" width="595" height="172" /></a></div>
    <p>I won't do any screenshots of the screen where you choose which servers to link because it is a very straight forward screen where you select the SQL Instance from the left side and click on the add button.</p>
    <p>At the start of this blog post, I mentioned that there are some change that would be really helpful.  What I meant by that is adding the option to include a second query that would be executed and the results displayed as a "drill in" option for the counter.</p>
    <p>You can also easily turn this counter into one for log backups</p>