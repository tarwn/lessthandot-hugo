---
title: Building a SharePoint 2013 BI Demo Environment Part 7 – Configuring SharePoint
author: Koen Verbeeck
type: post
date: 2013-06-07T13:27:00+00:00
ID: 2098
excerpt: 'In this blog post we go over one of the most important parts of the series: running the PowerPivot Configuration Wizard in order to configure our SharePoint installation. If you followed the previous walkthroughs, you can find the wizard in the start me&hellip;'
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/sharepoint-2013-bi-part-7/
views:
  - 41285
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
  - powerpivot configuration tool
  - sharepoint 2013
  - sql server
  - ssas
  - syndicated

---
<p style="text-align: justify;">
  In this blog post we go over one of the most important parts of the series: running the PowerPivot Configuration Wizard in order to configure our SharePoint installation. If you followed the previous walkthroughs, you can find the wizard in the start menu. It is not necessary to run the wizard, you can do everything yourself using the standard SharePoint configuration wizards. However, using the Configuration Wizard will install some extra components, such as scheduled data refresh, data providers and very important: the PowerPivot Management Dashboard. Read the following <a href="http://msdn.microsoft.com/en-us/library/jj682085.aspx">MSDN page</a> for more info.
</p>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part7/PowerPivotConfigWizard_00.png?mtime=1370373774"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part7/PowerPivotConfigWizard_00.png?mtime=1370373774" alt="" width="281" height="117" /></a>
</p>

<span style="text-align: justify;">If you get the following message when launching the wizard, it means you forgot to apply SQL Server 2012 service pack 1 to the PowerPivot instance (like I first did if you're wondering how I made the screenshot). So hurry back to </span><a style="text-align: justify;" href="/index.php/DataMgmt/business-intelligence-1/sharepoint-2013-bi-part-6">part 6 of the series</a> <span style="text-align: justify;">and apply that service pack!</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part7/PowerPivotConfigWizard_01.png?mtime=1370373774"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part7/PowerPivotConfigWizard_01.png?mtime=1370373774" alt="" width="391" height="152" /></a>
</p>

<span style="text-align: justify;">If the wizard does launch successfully, you have the choice to configure or repair the PowerPivot for SharePoint installation, or to remove or upgrade features, services, applications and solutions. Since it is the first time we are running the wizard, you can only configure. Click </span>_OK_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part7/PowerPivotConfigWizard_04.png?mtime=1370373774"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part7/PowerPivotConfigWizard_04.png?mtime=1370373774" alt="" width="461" height="205" /></a>
</p>

<span style="text-align: justify;">The wizard consists of a list of sections. In some sections you need to supply parameters, such as passwords or service accounts, in other sections you are not required to take action (indicated with green flags). If you have already setup a farm and configured some services, the wizard will detect this and will automatically fill in the necessary values. In our case we are dealing with a blank server. In each section you can also inspect the PowerShell script that eventually will do the actual configuration. It might come in handy if you want to do a scripted deployment in the future. Since the wizard executes the PowerShell statements behind the scenes, it is possible to configure SharePoint without the use of a domain controller. I leave it up to you to decide if you want to include a domain controller or not.</span>

<p style="text-align: justify;">
  I'll go over each section where input is needed, but a lot of parameters might have already default values specified.
</p>

<p style="text-align: justify;">
  The first one is <em>Configure New Farm</em>. Here we configure the database server, the farm account (see part 5) and the passphrase. As you can see, I used a local account for the farm account, not a domain account. In a simple demo environment like this it helps if you specify the same password/passphrase for everything. It makes your demos go a lot smoother! (The usual disclaimer: do not do this in production). Finally specify an easy to remember port for the SharePoint Central Administration site.
</p>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part7/PowerPivotConfigWizard_06.png?mtime=1370373774"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part7/PowerPivotConfigWizard_06.png?mtime=1370373774" alt="" width="638" height="254" /></a>
</p>

<span style="text-align: justify;">Next up is </span>_Create PowerPivot Service Application_<span style="text-align: justify;">. You need to specify a name for the service application, the database server and the name for the database. Some sections, like this one, are optional. If you do not want those configured, you can deselect the checkbox next to </span>_Include this action in the task list_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part7/PowerPivotConfigWizard_07.png?mtime=1370373775"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part7/PowerPivotConfigWizard_07.png?mtime=1370373775" alt="" width="656" height="229" /></a>
</p>

_Create Default Web Application_ <span style="text-align: justify;">follows in the list. Specify a name for the web application, an URL, the database server and a name for the database. As with most service accounts, I specified the farm account.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part7/PowerPivotConfigWizard_08.png?mtime=1370373775"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part7/PowerPivotConfigWizard_08.png?mtime=1370373775" alt="" width="533" height="241" /></a>
</p>

<span style="text-align: justify;">Check if the values specified at </span>_Deploy Web Application Solution_ <span style="text-align: justify;">satisfy your requirements.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part7/PowerPivotConfigWizard_09.png?mtime=1370373775"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part7/PowerPivotConfigWizard_09.png?mtime=1370373775" alt="" width="646" height="256" /></a>
</p>

<span style="text-align: justify;">You can create a site collection in </span>_Create Site Collection_ <span style="text-align: justify;">(they tend to keep things logical). Specify an URL, an administrator – the email address can be fake – and the title for the site.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part7/PowerPivotConfigWizard_10.png?mtime=1370373775"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part7/PowerPivotConfigWizard_10.png?mtime=1370373775" alt="" width="658" height="278" /></a>
</p>

<span style="text-align: justify;">If you want to add the PowerPivot features to the site, such as the carrousel in the PowerPivot library, select the checkbox in </span>_Activate PowerPivot Feature in a Site Collection_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part7/PowerPivotConfigWizard_11.png?mtime=1370373775"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part7/PowerPivotConfigWizard_11.png?mtime=1370373775" alt="" width="649" height="281" /></a>
</p>

<span style="text-align: justify;">Next we are going to configure the Secure Store Service, which is important if you want to work with unattended accounts. Specify a name for the Secure Store Service service application (what an alliteration) and the database server in </span>_Create Secure Store Service Application_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part7/PowerPivotConfigWizard_12.png?mtime=1370373776"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part7/PowerPivotConfigWizard_12.png?mtime=1370373776" alt="" width="661" height="343" /></a>
</p>

<span style="text-align: justify;">Also configure the Secure Store Service proxy in </span>_Create Secure Store Service Application Proxy_ <span style="text-align: justify;">(the defaults are fine).</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part7/PowerPivotConfigWizard_13.png?mtime=1370373776"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part7/PowerPivotConfigWizard_13.png?mtime=1370373776" alt="" width="621" height="363" /></a>
</p>

<span style="text-align: justify;">And specify a passphrase in </span>_Update Secure Store Service Master Key_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part7/PowerPivotConfigWizard_14.png?mtime=1370373776"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part7/PowerPivotConfigWizard_14.png?mtime=1370373776" alt="" width="664" height="368" /></a>
</p>

<span style="text-align: justify;">Finally specify an unattended account for the data refresh in </span>_Create Unattended Account for Data Refresh_<span style="text-align: justify;">. Configure the target application ID, a friendly name if you must, an account and the site URL.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part7/PowerPivotConfigWizard_15.png?mtime=1370373776"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part7/PowerPivotConfigWizard_15.png?mtime=1370373776" alt="" width="660" height="390" /></a>
</p>

<span style="text-align: justify;">Excel Services is next in the list. Specify a name for the service application in </span>_Create Excel Service Application_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part7/PowerPivotConfigWizard_16.png?mtime=1370373777"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part7/PowerPivotConfigWizard_16.png?mtime=1370373777" alt="" width="640" height="419" /></a>
</p>

<span style="text-align: justify;">Add the SSAS PowerPivot instance we installed in </span><a style="text-align: justify;" href="/index.php/DataMgmt/business-intelligence-1/sharepoint-2013-bi-part-6">part 6</a> <span style="text-align: justify;">to the Registered PowerPivot Servers in </span>_Configure PowerPivot Servers._

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part7/PowerPivotConfigWizard_17.png?mtime=1370373777"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part7/PowerPivotConfigWizard_17.png?mtime=1370373777" alt="" width="648" height="429" /></a>
</p>

<span style="text-align: justify;">Verify the Service Application Name in </span>_Register PowerPivot Addin as Excel Services Usage Tracker_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part7/PowerPivotConfigWizard_18.png?mtime=1370373777"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part7/PowerPivotConfigWizard_18.png?mtime=1370373777" alt="" width="624" height="441" /></a>
</p>

<span style="text-align: justify;">You get an overview of the most important information at the top, in </span>_Configure or Repair PowerPivot for SharePoint_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part7/PowerPivotConfigWizard_19.png?mtime=1370373777"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part7/PowerPivotConfigWizard_19.png?mtime=1370373777" alt="" width="663" height="250" /></a>
</p>

<span style="text-align: justify;">Click </span>_Validate_ <span style="text-align: justify;">at the bottom of the wizard to validate all your specifications. If everything is OK, all items get a green flag.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part7/PowerPivotConfigWizard_20.png?mtime=1370374793"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part7/PowerPivotConfigWizard_20.png?mtime=1370374793" alt="" width="687" height="559" /></a>
</p>

<span style="text-align: justify;">Click on </span>_Run_ <span style="text-align: justify;">to start the configuration of the SharePoint environment.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part7/PowerPivotConfigWizard_21.png?mtime=1370373803"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part7/PowerPivotConfigWizard_21.png?mtime=1370373803" alt="" width="275" height="135" /></a>
</p>

<p style="text-align: justify;">
  Once the configuration is done, you can go to the Central Administration site or your very own PowerPivot site to check if everything works. At first the sites might load a bit slowly, but once everything is in the cache it should go quicker.
</p>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part7/PowerPivotConfigWizard_23.png?mtime=1370373803"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part7/PowerPivotConfigWizard_23.png?mtime=1370373803" alt="" width="506" height="412" /></a>
</p>

<span style="text-align: justify;">In the next walkthrough, we'll go deeper into verifying if everything behaves as expected.</span>

Go back to [overview][1].   
Previous part: [Installing PowerPivot for SharePoint][2].   
Next part: [Verifying PowerPivot Integration][3].

 [1]: /index.php/DataMgmt/business-intelligence-1/sharepoint-2013-bi-part-1
 [2]: /index.php/DataMgmt/business-intelligence-1/sharepoint-2013-bi-part-6
 [3]: /index.php/DataMgmt/business-intelligence-1/sharepoint-2013-bi-part-8