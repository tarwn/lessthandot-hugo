---
title: Modify a Power View Data Source
author: Koen Verbeeck
type: post
date: 2013-05-08T08:11:00+00:00
ID: 2082
excerpt: This blog posts explains how you can modify the data source of a Power View report published on SharePoint.
url: /index.php/webdev/business-intelligence/modify-power-view-data-source/
views:
  - 35880
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
tags:
  - bi
  - credentials
  - data source
  - power view
  - sharepoint 2013
  - syndicated

---
<p style="text-align: justify;">
  I'm currently setting up a demo environment using SQL Server 2012 and SharePoint 2013 (more on that in later blog posts). This gives me the chance to play around with Power View for SharePoint and I absolutely love it. I ran into an issue however because the Windows Authentication wasn't working properly. When I view a Power View report I get the following error:
</p>

<p style="text-align: justify;">
  <em>An error occurred while loading the model for the item or data source 'EntityDataSource'. Verify that the connection information is correct and that you have permissions to access the data source.</em>
</p>

[<img src="/wp-content/uploads/users/koenverbeeck/PowerViewDataSource/errorbis.png?mtime=1367999572" alt="" width="802" height="361" />][1]

<span style="text-align: justify;">Not exactly what you want to encounter when giving a sales demo. So I had two options: either deal with Windows Token Claims / Kerberos or set up an unattended account to run the report. Since I'd rather jab myself in the eye with a rusty nail then dealing with Kerberos, I chose the last option. Setting up an unattended account is my preferred method for authentication, as it gives the least amount of troubles. I do it for Reporting Services reports and PowerPivot workbooks, so I'd also like to do it for Power View as well.</span>

<p style="text-align: justify;">
  So how do you modify a Power View data source? Luckily it's pretty straight forward if you know where to look. Go to the library where your Power View report is stored.
</p>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/PowerViewDataSource/library_01.png?mtime=1367999585"><img src="/wp-content/uploads/users/koenverbeeck/PowerViewDataSource/library_01.png?mtime=1367999585" alt="" width="608" height="375" /></a>
</p>

<span style="text-align: justify;">In the Library ribbon, set the Current View to </span>_All Documents._ <span style="text-align: justify;">Click on the down arrow of the report you wish to edit and select </span>_Manage Data Sources_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/PowerViewDataSource/library_02.png?mtime=1367999606"><img src="/wp-content/uploads/users/koenverbeeck/PowerViewDataSource/library_02.png?mtime=1367999606" alt="" width="567" height="306" /></a>
</p>

<span style="text-align: justify;">Click on </span>**EntityDataSource**<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/PowerViewDataSource/library_03.png?mtime=1367999616"><img src="/wp-content/uploads/users/koenverbeeck/PowerViewDataSource/library_03.png?mtime=1367999616" alt="" width="470" height="141" /></a>
</p>

<span style="text-align: justify;">Here we can finally modify the data source. You can choose a shared data source, modify the connection string (a PowerPivot workbook in my case) and set the credentials, which we are interested in. Fill in a user name and password under </span>_Stored Credentials_ <span style="text-align: justify;">to specify an unattended execution account. Don't forget to check the </span>_Use as Windows credentials_ <span style="text-align: justify;">checkbox.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/PowerViewDataSource/library_04.png?mtime=1368000128"><img src="/wp-content/uploads/users/koenverbeeck/PowerViewDataSource/library_04.png?mtime=1368000128" alt="" width="616" height="548" /></a>
</p>

<span style="text-align: justify;">Note: my demo environment has only one user: the Administrator. This is OK because it is – in fact – a demo environment. Please do not specify administrator credentials in a production environment (especially if you pay attention to the nice red warning on top). Use a dedicated domain account.</span>

<p style="text-align: justify;">
  Click OK and go back to your SharePoint page. The report now renders successfully! It is as easy as that.
</p>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/PowerViewDataSource/library_05.png?mtime=1368000136"><img src="/wp-content/uploads/users/koenverbeeck/PowerViewDataSource/library_05.png?mtime=1368000136" alt="" width="715" height="471" /></a>
</p>

 [1]: /media/users/koenverbeeck/PowerViewDataSource/errorbis.png?mtime=1367999572