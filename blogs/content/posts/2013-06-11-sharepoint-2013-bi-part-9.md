---
title: Building a SharePoint 2013 BI Demo Environment Part 9 – Installing Reporting Services
author: Koen Verbeeck
type: post
date: 2013-06-11T11:34:00+00:00
excerpt: 'Finally we are getting to the really sweet stuff: Power View. But first we have to install Reporting Services in SharePoint integrated mode. The steps we are going to follow are described in the following MSDN document: Install Reporting Services ShareP&hellip;'
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/sharepoint-2013-bi-part-9/
views:
  - 23597
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - Excel Reporting
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
  - SSAS
  - SSRS
tags:
  - business intelligence
  - demo environment
  - excel
  - power view
  - powerpivot
  - sharepoint 2013
  - sql server
  - ssas
  - ssrs
  - syndicated

---
<p style="text-align: justify;">
  Finally we are getting to the really sweet stuff: Power View. But first we have to install Reporting Services in SharePoint integrated mode. The steps we are going to follow are described in the following MSDN document: <a href="http://msdn.microsoft.com/en-us/library/jj219068.aspx">Install Reporting Services SharePoint Mode for SharePoint 2013</a>.
</p>

<p style="text-align: justify;">
  Attach the SQL Server image to the virtual machine and launch the SQL Server setup. Go through the usual steps of the setup wizard until you need to choose the installation type. Choose <em>Add features to an existing instance of SQL Server 2012</em> and make sure you select the correct instance from the dropdown if you have multiple instances on your machine. Click <em>Next</em>.
</p>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part9/InstallSSRS_01.png?mtime=1370459857"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part9/InstallSSRS_01.png?mtime=1370459857" alt="" width="487" height="365" /></a>
</p>

<span style="text-align: justify;">In the Feature Selection step, choose </span>**Reporting Services – SharePoint** <span style="text-align: justify;">and </span>**Reporting Services Add-in for SharePoint Products**<span style="text-align: justify;">. The add-in will for example include the Power View feature.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part9/InstallSSRS_02.png?mtime=1370459858"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part9/InstallSSRS_02.png?mtime=1370459858" alt="" width="487" height="365" /></a>
</p>

<p style="text-align: justify;">
  Go over the installation rules, check the disk space requirements and go to the Reporting Services Configuration. Here you simply have the option <em>Install Only</em>. Click <em>Next</em>.
</p>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part9/InstallSSRS_04.png?mtime=1370459858"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part9/InstallSSRS_04.png?mtime=1370459858" alt="" width="487" height="365" /></a>
</p>

<span style="text-align: justify;">Skip the Error Reporting, check the installation configuration rules and take a look at the installation overview. If everything seems OK, hit </span>_Install_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part9/InstallSSRS_07.png?mtime=1370459858"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part9/InstallSSRS_07.png?mtime=1370459858" alt="" width="487" height="365" /></a>
</p>

<span style="text-align: justify;">After all of this hard work, you deserve a coffee…</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part9/InstallSSRS_08.png?mtime=1370459858"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part9/InstallSSRS_08.png?mtime=1370459858" alt="" width="487" height="365" /></a>
</p>

<span style="text-align: justify;">After a while the installation will be finished. Click </span>_Close_ <span style="text-align: justify;">to finish the wizard.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part9/InstallSSRS_09.png?mtime=1370459859"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part9/InstallSSRS_09.png?mtime=1370459859" alt="" width="487" height="365" /></a>
</p>

<span style="text-align: justify;">Once Reporting Services is installed, we need to register and start the Reporting Services SharePoint Service. This can be done with the PowerShell commands </span>_Install-SPRSService_ <span style="text-align: justify;">and </span>_Install-SPRSServiceProxy_ <span style="text-align: justify;">in the </span>**SharePoint 2013 Management Shell**<span style="text-align: justify;">. However, the first command immediately fails with the following error:</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part9/InstallSSRS_10.png?mtime=1370459859"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part9/InstallSSRS_10.png?mtime=1370459859" alt="" width="468" height="236" /></a>
</p>

<span style="text-align: justify;">What’s the problem? We forgot to update the Reporting Services installation with service pack 1! So launch the sp1 setup and go through the wizard.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part9/InstallSSRS_12.png?mtime=1370459859"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part9/InstallSSRS_12.png?mtime=1370459859" alt="" width="487" height="365" /></a>
</p>

<span style="text-align: justify;">You also might want to run the KB again we discussed in </span><a style="text-align: justify;" href="/index.php/DataMgmt/business-intelligence-1/sharepoint-2013-bi-part-4">part 4</a><span style="text-align: justify;">. It’s possible a reboot is needed after these updates.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part9/InstallSSRS_13.png?mtime=1370459859"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part9/InstallSSRS_13.png?mtime=1370459859" alt="" width="487" height="365" /></a>
</p>

<span style="text-align: justify;">Run the two PowerShell command again, who will run without a problem this time.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part9/InstallSSRS_15.png?mtime=1370459859"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part9/InstallSSRS_15.png?mtime=1370459859" alt="" width="505" height="89" /></a>
</p>

<span style="text-align: justify;">When we check the services on the SharePoint server (Central Administration > System Settings > Manage services on server), we can now see the </span>_SQL Server Reporting Services Service_<span style="text-align: justify;">, which is in the stopped state. Click on </span>_Start_ <span style="text-align: justify;">to start the service.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part9/InstallSSRS_14.png?mtime=1370459859"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part9/InstallSSRS_14.png?mtime=1370459859" alt="" width="763" height="507" /></a>
</p>

<span style="text-align: justify;">The next step is to create a </span>_SQL Server Reporting Services Service Application_<span style="text-align: justify;">, or SSRSSA as we call it. Go to </span>_Manage service applications_ <span style="text-align: justify;">under Application Management. In the ribbon, click </span>_New_ <span style="text-align: justify;">and select </span>_SQL Server Reporting Services Service Application_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part9/InstallSSRS_16.png?mtime=1370459859"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part9/InstallSSRS_16.png?mtime=1370459859" alt="" width="263" height="252" /></a>
</p>

<div class="image_block">
  Type a name, choose to create a new application pool and select the SPFarm account as the security account.
</div>

[<img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part9/InstallSSRS_17.png?mtime=1370459859" alt="" width="492" height="391" />][1]

<span style="text-align: justify;">Configure the database server, the database name and the stored credentials. For the Web Application Assocation, choose </span><a style="text-align: justify;" href="http://sp2013bi/">http://sp2013bi</a><span style="text-align: justify;">. This is the only one we created in this walkthrough.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part9/InstallSSRS_18.png?mtime=1370459860"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part9/InstallSSRS_18.png?mtime=1370459860" alt="" width="487" height="389" /></a>
</p>

<span style="text-align: justify;">Click </span>_OK_ <span style="text-align: justify;">to create the service application.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part9/InstallSSRS_19.png?mtime=1370459860"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part9/InstallSSRS_19.png?mtime=1370459860" alt="" width="525" height="120" /></a>
</p>

<span style="text-align: justify;">The final step is to active the </span>**Power View Site Collection Feature**<span style="text-align: justify;">. Browse to your SharePoint site and go to the Site Settings. Click on </span>_Site collection features._

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part9/InstallSSRS_21.png?mtime=1370459879"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part9/InstallSSRS_21.png?mtime=1370459879" alt="" width="402" height="515" /></a>
</p>

<span style="text-align: justify;">Locate the </span>_Power View Integration Feature_ <span style="text-align: justify;">and activate it if it isn’t already.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part9/InstallSSRS_22.png?mtime=1370459879"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part9/InstallSSRS_22.png?mtime=1370459879" alt="" width="1011" height="142" /></a>
</p>

<span style="text-align: justify;">The installation and configuration of Reporting Services and Power View is behind us, so now you can create some dazzling reports!</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part9/InstallSSRS_24.png?mtime=1370459879"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part9/InstallSSRS_24.png?mtime=1370459879" alt="" width="559" height="436" /></a>
</p>

<p style="text-align: justify;">
  Go back to <a href="/index.php/DataMgmt/business-intelligence-1/sharepoint-2013-bi-part-1">overview</a>.<br />Previous part: <a href="/index.php/DataMgmt/business-intelligence-1/sharepoint-2013-bi-part-8">Verifying PowerPivot Integration</a>.<br />Next part: <a href="/index.php/DataMgmt/business-intelligence-1/sharepoint-2013-bi-part-10">Creating a BI site</a>.
</p>

 [1]: /media/users/koenverbeeck/SP2013DemoEnv/Part9/InstallSSRS_17.png?mtime=1370459859