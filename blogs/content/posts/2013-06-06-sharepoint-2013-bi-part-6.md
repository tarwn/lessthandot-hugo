---
title: Building a SharePoint 2013 BI Demo Environment Part 6 – Installing PowerPivot for SharePoint
author: Koen Verbeeck
type: post
date: 2013-06-06T12:14:00+00:00
ID: 2097
excerpt: |
  In this part of the series we will install the Analysis Service instance that will host the PowerPivot workbooks used in SharePoint, also known as “the PowerPivot instance”.
  The first step is to create a service account for this instance. I named it SP&hellip;
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/sharepoint-2013-bi-part-6/
views:
  - 22281
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
  - powerpivot
  - sharepoint 2013
  - sql server
  - ssas
  - syndicated

---
<p style="text-align: justify;">
  Before we can configure SharePoint, we will have to install the Analysis Service instance that will host the PowerPivot workbooks used in SharePoint, also known as “the PowerPivot instance”.
</p>

<p style="text-align: justify;">
  The first step is to create a service account for this instance. I named it SP2013BISPPowerPivot.
</p>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part6/InstallPowerPivotInstance_00.png?mtime=1370293230"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part6/InstallPowerPivotInstance_00.png?mtime=1370293230" alt="" width="273" height="270" /></a>
</p>

<span style="text-align: justify;">The next step is attaching the SQL Server image to our virtual machine and launching the SQL Server setup. Go through the usual steps of the setup wizard until you need to choose the installation type. Choose the type </span>_Perform a new installation of SQL Server 2012_ <span style="text-align: justify;">and click </span>_Next_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part6/InstallPowerPivotInstance_01.png?mtime=1370293242"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part6/InstallPowerPivotInstance_01.png?mtime=1370293242" alt="" width="486" height="366" /></a>
</p>

Enter the product key and accept the license terms.

<span style="text-align: justify;">Choose the </span>**SQL Server PowerPivot for SharePoint** <span style="text-align: justify;">setup role. We already have a SQL Server instance installed, so you can deselect the checkbox for adding another instance. Click </span>_Next_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part6/InstallPowerPivotInstance_04.png?mtime=1370293274"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part6/InstallPowerPivotInstance_04.png?mtime=1370293274" alt="" width="488" height="364" /></a>
</p>

<span style="text-align: justify;">Everything is already selected in the Feature Selection page, so just click </span>_Next_ <span style="text-align: justify;">to go to the next step.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part6/InstallPowerPivotInstance_05.png?mtime=1370293282"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part6/InstallPowerPivotInstance_05.png?mtime=1370293282" alt="" width="486" height="364" /></a>
</p>

<span style="text-align: justify;">A set of operation rules will run. It will for example check if the PowerPivot instance name has not already been taken. If everything is validated, click </span>_Next_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part6/InstallPowerPivotInstance_06.png?mtime=1370293291"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part6/InstallPowerPivotInstance_06.png?mtime=1370293291" alt="" width="484" height="364" /></a>
</p>

<span style="text-align: justify;">In the Instance Configuration page, leave everything as default and click </span>_Next_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part6/InstallPowerPivotInstance_07.png?mtime=1370293305"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part6/InstallPowerPivotInstance_07.png?mtime=1370293305" alt="" width="486" height="363" /></a>
</p>

<span style="text-align: justify;">Review the disk space requirements and go to the Server Configuration page. Configure the SPPowerPivot account as the service account for our Analysis Services instance. Leave the collation to the default. Click </span>_Next_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part6/InstallPowerPivotInstance_09.png?mtime=1370293323"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part6/InstallPowerPivotInstance_09.png?mtime=1370293323" alt="" width="490" height="366" /></a>
</p>

<span style="text-align: justify;">Add yourself and the SharePoint farm account as administrators. If you have multiple drives, you can optionally move the data and log files to another disk. Click </span>_Next_ <span style="text-align: justify;">twice.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part6/InstallPowerPivotInstance_10.png?mtime=1370293331"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part6/InstallPowerPivotInstance_10.png?mtime=1370293331" alt="" width="487" height="364" /></a>
</p>

<span style="text-align: justify;">The Installation Rules will run. If every rule is validated, click </span>_Next_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part6/InstallPowerPivotInstance_12.png?mtime=1370293353"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part6/InstallPowerPivotInstance_12.png?mtime=1370293353" alt="" width="488" height="364" /></a>
</p>

<span style="text-align: justify;">Review the installation and click </span>_Install_ <span style="text-align: justify;">if you’re satisfied.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part6/InstallPowerPivotInstance_13.png?mtime=1370293361"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part6/InstallPowerPivotInstance_13.png?mtime=1370293361" alt="" width="487" height="363" /></a>
</p>

<span style="text-align: justify;">Go grab a beverage of your choice while the SSAS instance is being installed…</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part6/InstallPowerPivotInstance_14.png?mtime=1370293368"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part6/InstallPowerPivotInstance_14.png?mtime=1370293368" alt="" width="485" height="362" /></a>
</p>

<span style="text-align: justify;">Once the installation is finished, click </span>_Close_ <span style="text-align: justify;">to finish the setup.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part6/InstallPowerPivotInstance_15.png?mtime=1370293381"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part6/InstallPowerPivotInstance_15.png?mtime=1370293381" alt="" width="486" height="364" /></a>
</p>

<span style="text-align: justify;">There is now a SSAS instance called PowerPivot running on the server. When your SharePoint environment is finished and you have PowerPivot workbooks in SharePoint, you get the following list of databases when you log into the instance:</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part6/InstallPowerPivotInstance_16.png?mtime=1370293385"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part6/InstallPowerPivotInstance_16.png?mtime=1370293385" alt="" width="481" height="200" /></a>
</p>

<span style="text-align: justify;">Each PowerPivot workbook is a Tabular Model – with a bunch of gibberish appended to the workbook name – stored in the instance. The interaction is limited though. For example you cannot process the tables.</span>

<p style="text-align: justify;">
  The last piece is running SQL Server 2012 service pack 1 again. Run the sp1 setup and make sure the PowerPivot instance is selected.
</p>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part6/PowerPivotConfigWizard_02.png?mtime=1370294004"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part6/PowerPivotConfigWizard_02.png?mtime=1370294004" alt="" width="488" height="364" /></a>
</p>

<span style="text-align: justify;">After the installation, finish the setup.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part6/PowerPivotConfigWizard_03.png?mtime=1370294040"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part6/PowerPivotConfigWizard_03.png?mtime=1370294040" alt="" width="488" height="364" /></a>
</p>

<span style="text-align: justify;">We are now ready to launch the PowerPivot Configuration Wizard, which will be discussed in the following part of the series.</span>

Go back to [overview][1].   
Previous part: [Installing SharePoint][2].   
Next part: [Configuring SharePoint][3].

 [1]: /index.php/DataMgmt/business-intelligence-1/sharepoint-2013-bi-part-1
 [2]: /index.php/DataMgmt/business-intelligence-1/sharepoint-2013-bi-part-5
 [3]: /index.php/DataMgmt/business-intelligence-1/sharepoint-2013-bi-part-7