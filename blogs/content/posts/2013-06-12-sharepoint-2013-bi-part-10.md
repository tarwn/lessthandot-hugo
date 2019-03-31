---
title: Building a SharePoint 2013 BI Demo Environment Part 10 – Creating a BI site
author: Koen Verbeeck
type: post
date: 2013-06-12T11:59:00+00:00
excerpt: 'In the final part of this series, we will create a BI subsite in our site collection. You can use this site as the starting point for your SharePoint BI demos. Browse to the PowerPivot site we created earlier in part 7 and go to Site Contents. Click on&hellip;'
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/sharepoint-2013-bi-part-10/
views:
  - 36383
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
  In the final part of this series, we will create a BI subsite in our site collection. You can use this site as the starting point for your SharePoint BI demos. Browse to the PowerPivot site we created earlier in <a href="/index.php/DataMgmt/business-intelligence-1/sharepoint-2013-bi-part-7">part 7</a> and go to <em>Site Contents</em>. Click on the <em>new subsite</em> link to create a new SharePoint site.
</p>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part10/CreateSite_01.png?mtime=1370461325"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part10/CreateSite_01.png?mtime=1370461325" alt="" width="335" height="373" /></a>
</p>

<span style="text-align: justify;">In the following dialog, configure the name, URL, permissions and navigation. Select the </span>**Business Intelligence Center** <span style="text-align: justify;">template, listed under Enterprise. Click </span>_Create_ <span style="text-align: justify;">to finish the configuration.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part10/CreateSite_02.png?mtime=1370461325"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part10/CreateSite_02.png?mtime=1370461325" alt="" width="445" height="732" /></a>
</p>

<span style="text-align: justify;">Hold on! An error? It seems we need to activate the </span>_SharePoint Publishing Infrastructure_ <span style="text-align: justify;">feature at the collection level before we can create the site.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part10/CreateSite_03.png?mtime=1370461325"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part10/CreateSite_03.png?mtime=1370461325" alt="" width="638" height="244" /></a>
</p>

<span style="text-align: justify;">So let’s activate this feature. Go to Site Settings > Site Collection Features and activate the </span>**SharePoint Server Publishing Infrastructure** <span style="text-align: justify;">feature.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part10/CreateSite_06.png?mtime=1370461325"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part10/CreateSite_06.png?mtime=1370461325" alt="" width="1000" height="107" /></a>
</p>

<span style="text-align: justify;">Next go to Site Settings > Site Features and activate </span>**SharePoint Server Publishing** **if it isn&#8217;t already active**<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part10/CreateSite_04.png?mtime=1370461325"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part10/CreateSite_04.png?mtime=1370461325" alt="" width="996" height="105" /></a>
</p>

<span style="text-align: justify;">Go back to create a BI subsite, fill in the details and hit </span>_Create_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part10/CreateSite_07.png?mtime=1370461325"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part10/CreateSite_07.png?mtime=1370461325" alt="" width="607" height="203" /></a>
</p>

<span style="text-align: justify;">What? Another error! The Performance Point features must also be activated before you can create a BI site. (I hope you are reading through this blog first before you actually try it out). Although I will probably not use Performance Point – like many other – I still need to enable it in order to create my site. So let’s do this.</span>

<p style="text-align: justify;">
  Go to Central Administration > System Settings > Manage services on server and activate the Performance Point Service.
</p>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part10/EnablePerformancePoint_01.png?mtime=1370461326"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part10/EnablePerformancePoint_01.png?mtime=1370461326" alt="" width="893" height="402" /></a>
</p>

<span style="text-align: justify;">Next go to Application Management > Manage service applications. In the ribbon, click </span>_New_ <span style="text-align: justify;">and select </span>**PerformancePoint Service Application**<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part10/EnablePerformancePoint_02.png?mtime=1370461326"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part10/EnablePerformancePoint_02.png?mtime=1370461326" alt="" width="198" height="304" /></a>
</p>

<span style="text-align: justify;">Configure the name for the service application and the database server.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part10/EnablePerformancePoint_02a.png?mtime=1370461326"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part10/EnablePerformancePoint_02a.png?mtime=1370461326" alt="" width="425" height="417" /></a>
</p>

<span style="text-align: justify;">Create a new application pool and specify the security account. Click </span>_Create_ <span style="text-align: justify;">to finish.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part10/EnablePerformancePoint_02b.png?mtime=1370461326"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part10/EnablePerformancePoint_02b.png?mtime=1370461326" alt="" width="414" height="407" /></a>
</p>

<span style="text-align: justify;">When the creation was successful, you’ll get a list of possible follow-up actions. Click </span>_OK_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part10/EnablePerformancePoint_03.png?mtime=1370461326"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part10/EnablePerformancePoint_03.png?mtime=1370461326" alt="" width="375" height="345" /></a>
</p>

<span style="text-align: justify;">Go to Site Settings > Site Collection Features and enable the </span>**PerformancePoint Services Site Collection Features**<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part10/EnablePerformancePoint_04a.png?mtime=1370461327"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part10/EnablePerformancePoint_04a.png?mtime=1370461327" alt="" width="1181" height="124" /></a>
</p>

<span style="text-align: justify;">Next go to Application Management > Manage service applications. Click on the </span>_PerformancePoint Service Application_ <span style="text-align: justify;">and go to its settings.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part10/EnablePerformancePoint_05a.png?mtime=1370461327"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part10/EnablePerformancePoint_05a.png?mtime=1370461327" alt="" width="788" height="131" /></a>
</p>

<span style="text-align: justify;">Configure the unattended service account to SPFarm and click </span>_OK_ <span style="text-align: justify;">at the bottom.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part10/EnablePerformancePoint_05.png?mtime=1370461327"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part10/EnablePerformancePoint_05.png?mtime=1370461327" alt="" width="841" height="228" /></a>
</p>

<span style="text-align: justify;">Finally go to Application Management. Click on </span>_Configure service application assocations_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part10/EnablePerformancePoint_06.png?mtime=1370461327"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part10/EnablePerformancePoint_06.png?mtime=1370461327" alt="" width="656" height="200" /></a>
</p>

<span style="text-align: justify;">Verify if PerformancePoint is correctly associated with the web application.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part10/EnablePerformancePoint_07.png?mtime=1370461327"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part10/EnablePerformancePoint_07.png?mtime=1370461327" alt="" width="724" height="159" /></a>
</p>

<span style="text-align: justify;">Now PerformancePoint is enabled, you can finally create your BI site! Repeat the steps from the beginning of this blog post to create your site.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part10/CreateSite_08.png?mtime=1370461325"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part10/CreateSite_08.png?mtime=1370461325" alt="" width="613" height="322" /></a>
</p>

<span style="text-align: justify;">All that’s left is to adjust this site to your preferences. This is for example how my Power View library looks like at the time of writing.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part10/CreateSite_09.png?mtime=1370461326"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part10/CreateSite_09.png?mtime=1370461326" alt="" width="906" height="564" /></a>
</p>

<span style="text-align: justify;">If you want to use a SharePoint list as an OData source for your demos, you don’t need to install ADO.NET data services like in SharePoint 2010. This functionality is already present.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part10/AlreadyInstalled.png?mtime=1370462164"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part10/AlreadyInstalled.png?mtime=1370462164" alt="" width="606" height="292" /></a>
</p>

<span style="text-align: justify;">This concludes the series on how to configure your own SharePoint 2013 BI demo environment! Have fun with creating some great demos!</span>

<p style="text-align: justify;">
  <span style="text-align: justify;">Go back to </span><a style="text-align: justify;" href="/index.php/DataMgmt/business-intelligence-1/sharepoint-2013-bi-part-1">overview</a><span style="text-align: justify;">.</span><br style="text-align: justify;" /><span style="text-align: justify;">Previous part: <a href="/index.php/DataMgmt/business-intelligence-1/sharepoint-2013-bi-part-9">Installing Reporting Services</a></span><span style="text-align: justify;">.</span>
</p>