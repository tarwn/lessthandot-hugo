---
title: Building a SharePoint 2013 BI Demo Environment Part 8 – Verifying PowerPivot Integration
author: Koen Verbeeck
type: post
date: 2013-06-10T12:08:00+00:00
ID: 2099
excerpt: 'After configuring SharePoint in the part 7, we will now verify if the configuration was successful and if everything works correctly. The verification process is described in the following MSDN article: Verify a PowerPivot for SharePoint Installation. F&hellip;'
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/sharepoint-2013-bi-part-8/
views:
  - 27681
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
  After configuring SharePoint in the <a href="/index.php/DataMgmt/business-intelligence-1/sharepoint-2013-bi-part-7">part 7</a>, we will now verify if the configuration was successful and if everything works correctly. The verification process is described in the following MSDN article: <a href="http://msdn.microsoft.com/en-us/library/hh231684.aspx">Verify a PowerPivot for SharePoint Installation</a>. First of all let's start by verifying the site level integration. Navigate to our newly created PowerPivot site, <a href="http://sp2013bi/">http://sp2013bi</a>.
</p>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_01.png?mtime=1370375907"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_01.png?mtime=1370375907" alt="" width="619" height="358" /></a>
</p>

<span style="text-align: justify;">Click on the </span>_PowerPivot Gallery_ <span style="text-align: justify;">link in the navigation menu. You probably get the Silverlight icon, telling you to install it. Click on the icon to start the installation process. You might have to refresh the browser after the setup to see take it effect. This step is necessary, the PowerPivot libraries and Power View make use of this technology.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_02.png?mtime=1370375907"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_02.png?mtime=1370375907" alt="" width="272" height="118" /></a>
</p>

<span style="text-align: justify;">When you upload some PowerPivot workbooks to the PowerPivot gallery, you should get some snapshots on the workbook preview. However, sometimes they do not work.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_21.png?mtime=1370375950"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_21.png?mtime=1370375950" alt="" width="498" height="233" /></a>
</p>

<span style="text-align: justify;">This can be caused by a multitude of reasons, of which a bunch are listed in </span><a style="text-align: justify;" href="http://support.microsoft.com/kb/2361559">this KB</a><span style="text-align: justify;">. The example I used probably suffered from the problem described in point 7: "The workbook might contain more than one data source. If so, a thumbnail will not be generated." I uploaded another PowerPivot workbook with a simple pivot on AdventureWorks, and this one worked immediately:</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_22.png?mtime=1370375951"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_22.png?mtime=1370375951" alt="" width="994" height="448" /></a>
</p>

<span style="text-align: justify;">Let's proceed with the verification. In the navigation menu, click on </span>_Site Contents_<span style="text-align: justify;">. Next click on </span>_add an app_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_02a.png?mtime=1370375907"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_02a.png?mtime=1370375907" alt="" width="205" height="164" /></a>
</p>

<span style="text-align: justify;">If you can see the </span>**Data Feed Library** <span style="text-align: justify;">and </span>**PowerPivot Gallery** <span style="text-align: justify;">in the list, the PowerPivot feature is integrated correctly.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_02b.png?mtime=1370375907"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_02b.png?mtime=1370375907" alt="" width="260" height="128" /></a>
</p>

<span style="text-align: justify;">Next up is the Central Administration integration. Navigate to the Central Admin site on the following URL: </span><a style="text-align: justify;" href="http://sp2013bi:2000/">http://sp2013bi:2000</a><span style="text-align: justify;">. Now you can see why you had to choose an easy-to-remember port in the SharePoint configuration.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_03.png?mtime=1370375907"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_03.png?mtime=1370375907" alt="" width="692" height="438" /></a>
</p>

<span style="text-align: justify;">Click on </span>_Manage Farm Features_ <span style="text-align: justify;">in the System Settings section. The </span>**PowerPivot Integration Feature** <span style="text-align: justify;">should be active.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_04.png?mtime=1370375907"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_04.png?mtime=1370375907" alt="" width="1005" height="487" /></a>
</p>

<span style="text-align: justify;">Back in the Central Admin, click on </span>_Manage Services on Server_<span style="text-align: justify;">. The </span>**SQL Server PowerPivot System Service** <span style="text-align: justify;">should be started. The documentation also mentions also a SQL Server Analysis Services service, however this service cannot be found in the list. I don't have a SharePoint 2010 installation to verify if it is there in that edition, but you don't seem to need it in SharePoint 2013. We do know for sure the PowerPivot SSAS service, installed in </span><a style="text-align: justify;" href="/index.php/DataMgmt/business-intelligence-1/sharepoint-2013-bi-part-6">part 6</a><span style="text-align: justify;">, is running.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_05.png?mtime=1370375908"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_05.png?mtime=1370375908" alt="" width="751" height="484" /></a>
</p>

<span style="text-align: justify;">The next step is </span>_Manage Service Applications_ <span style="text-align: justify;">in the Application Management section. The Excel Services application should be started, but more important, so should also be the </span>**Default PowerPivot Service Appplication**<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_06.png?mtime=1370375908"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_06.png?mtime=1370375908" alt="" width="1008" height="186" /></a>
</p>

<span style="text-align: justify;">Clicking on the application brings you to the </span>**PowerPivot Management Dashboard**<span style="text-align: justify;">, a key instrument for the IT department to guide self-service BI. However, it is empty.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_07.png?mtime=1370375908"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_07.png?mtime=1370375908" alt="" width="803" height="445" /></a>
</p>

<span style="text-align: justify;">This might be caused by the fact you haven't done much PowerPivot related activity on your server, but also because the </span>_PowerPivot Management Dashboard Processing Timer Job_ <span style="text-align: justify;">(quite a mouthful) hasn't run yet and thus hasn't collected any data yet. So click on </span>_Review timer job definitions_ <span style="text-align: justify;">to take a look at the job schedules. You'll see the Dashboard job has a daily schedule.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_08.png?mtime=1370375908"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_08.png?mtime=1370375908" alt="" width="928" height="172" /></a>
</p>

<span style="text-align: justify;">Change this to an hourly schedule by clicking on the jobs name to go to the edit screen.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_08a.png?mtime=1370375908"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_08a.png?mtime=1370375908" alt="" width="447" height="435" /></a>
</p>

<span style="text-align: justify;">Click on </span>_Run Now_ <span style="text-align: justify;">to launch the job immediately. You'll have to wait for a few minutes until the job has finished and the dashboard has been updated. However, in my case, the dashboard stayed empty. So I investigated the issue! In the job definitions menu, you can click on the </span>_Job History_ <span style="text-align: justify;">link at the left of the screen.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_08b.png?mtime=1370375908"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_08b.png?mtime=1370375908" alt="" width="129" height="124" /></a>
</p>

<span style="text-align: justify;">Here I could see the job had failed!</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_09.png?mtime=1370375908"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_09.png?mtime=1370375908" alt="" width="762" height="148" /></a>
</p>

<span style="text-align: justify;">Time to troubleshoot! I used the free download </span><a style="text-align: justify;" href="http://ulsviewer.codeplex.com/">ULS Viewer</a> <span style="text-align: justify;">on Codeplex. After filtering on </span>_Excel_<span style="text-align: justify;">, I saw the following exception:</span>

<p style="text-align: justify;">
  <em>The data connection uses Windows Authentication and user credentials could not be deletegated</em>.
</p>

<p style="text-align: justify;">
  Or in other words: you did not configure a domain controller stupid! This is the one place where it has an actual downside of not having a domain controller configured. This issue could have other possible causes as well. This blog post does a nice job of listing most of them: <a href="http://powerpivotgeek.com/2010/02/08/the-data-connection-uses-windows-authentication-and-user-credentials-could-not-be-delegated/">The data connection uses Windows Authentication and user credentials could not be delegated</a>.
</p>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_10.png?mtime=1370375909"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_10.png?mtime=1370375909" alt="" width="617" height="325" /></a>
</p>

<span style="text-align: justify;">In order to solve this issue, we need to use an unattended account to run the PowerPivot Management Dashboard. We can do this in the Excel Services application. Go to </span>_Global Settings_ <span style="text-align: justify;">and set the Target Application ID to </span>**PowerPivotUnattendedAccount** <span style="text-align: justify;">(which we configured in </span><a style="text-align: justify;" href="/index.php/DataMgmt/business-intelligence-1/sharepoint-2013-bi-part-7">part 7</a><span style="text-align: justify;">) in the External Data section.</span>

<p style="text-align: justify;">
  <img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_11.png?mtime=1370375909" alt="" width="1009" height="437" />
</p>

<p style="text-align: justify;">
  <strong> </strong>
</p>

<p style="text-align: justify;">
  Unfortunately, this only solves a part of the problem. If you run the timer job again, you can now see the Activity Chart is populated with one nice little bubble, which represents the dashboard itself. But the Server Health is still very empty. This is caused by the fact that this chart uses some Excel workbooks behind the scenes. These workbooks need to be configured themselves to use an unattended account as well. This procedure is described in the following blog post: <a href="http://powerpivotgeek.com/2009/11/06/taking-your-server-off-the-network/">Taking your PowerPivot server off the network</a>. It is written for people using PowerPivot when disconnected – for example on an airplane – and thus have no connection to a domain controller. But it also works for people stubborn enough not to install a domain controller. Such as me.
</p>

<p style="text-align: justify;">
  The process is simple. When in the PowerPivot Management Dashboard, click on <em>Site Contents</em>. There go to the <em>PowerPivot Management</em> library.
</p>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_13.png?mtime=1370375909"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_13.png?mtime=1370375909" alt="" width="217" height="326" /></a>
</p>

<span style="text-align: justify;">Click on the GUID.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_14.png?mtime=1370375909"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_14.png?mtime=1370375909" alt="" width="471" height="147" /></a>
</p>

<span style="text-align: justify;">You'll see the PowerPivot Management Data workbook and a folder called 1033. In the folder you will find a connection and two workbooks, Server Health and Workbook Activity.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_15.png?mtime=1370375909"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_15.png?mtime=1370375909" alt="" width="546" height="181" /></a>
</p>

<span style="text-align: justify;">Click on one of the workbooks to open it in Excel Services. There, click on </span>_Open in Excel_<span style="text-align: justify;">, which will open the workbook locally on your computer.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_16.png?mtime=1370375909"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_16.png?mtime=1370375909" alt="" width="719" height="235" /></a>
</p>

<span style="text-align: justify;">Go to the Data tab and click on </span>_Connections_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_17.png?mtime=1370375950"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_17.png?mtime=1370375950" alt="" width="154" height="124" /></a>
</p>

<span style="text-align: justify;">There will be only one connection, called </span>**Data**<span style="text-align: justify;">. Go to its properties. In the connection properties window, go to the Definition tab and click on </span>_Authentication Settings_ <span style="text-align: justify;">for Excel Services.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_18.png?mtime=1370375950"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_18.png?mtime=1370375950" alt="" width="345" height="387" /></a>
</p>

<span style="text-align: justify;">Set the account to use to </span>_None_<span style="text-align: justify;">. This will force Excel Services to use the unattended account.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_19.png?mtime=1370375950"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_19.png?mtime=1370375950" alt="" width="242" height="200" /></a>
</p>

<span style="text-align: justify;">Click </span>_OK_ <span style="text-align: justify;">and repeat the same process for the other workbook. You might get an error about the name for the connection being a reserved name. If this is the case, change it to something else and save. Excel will change the name back automatically. When we now run the timer job again and go to the PowerPivot Management Dashboard, we have a fully populated dashboard!</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_19a.png?mtime=1370375950"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part8/VerifyPowerPivot_19a.png?mtime=1370375950" alt="" width="982" height="444" /></a>
</p>

<span style="text-align: justify;">This concludes the PowerPivot integration verification. In the next part of this series, we will play with even more awesome features: Power View!</span>

<p style="text-align: justify;">
  Go back to <a href="/index.php/DataMgmt/business-intelligence-1/sharepoint-2013-bi-part-1">overview</a>.<br />Previous part: <a href="/index.php/DataMgmt/business-intelligence-1/sharepoint-2013-bi-part-7">Configuring SharePoint</a>.<br />Next part: <a href="/index.php/DataMgmt/business-intelligence-1/sharepoint-2013-bi-part-9">Installing Reporting Services</a>.
</p>