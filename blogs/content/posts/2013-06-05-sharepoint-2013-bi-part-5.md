---
title: Building a SharePoint 2013 BI Demo Environment Part 5 – Installing SharePoint
author: Koen Verbeeck
type: post
date: 2013-06-05T11:57:00+00:00
ID: 2096
excerpt: |
  Let’s get started with the next step in the series: installing SharePoint. We will install only the software, but we do not configure it. We will do this later with the PowerPivot Configuration Tool, which is explained in another blog post.
  The first s&hellip;
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/sharepoint-2013-bi-part-5/
views:
  - 22395
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
  - sharepoint 2013
  - sql server
  - syndicated
  - virtual machine

---
<p style="text-align: justify;">
  Let’s get started with the next step in the series: installing SharePoint. We will install only the software, but we do not configure it. We will do this later with the PowerPivot Configuration Tool, which is explained in another blog post.
</p>

<p style="text-align: justify;">
  The first step is to create a farm account. This is the most important account in your SharePoint environment. I made this account administrator on the virtual machine and also sysadmin in the SQL Server instance. Normally the SharePoint set-up will add the farm account to SQL Server and give it appropriate permissions, but since this is a simple demo environment I just played it safe. We will use this account when we configure SharePoint in a later blog post.
</p>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part5/InstallSharePoint_00.png?mtime=1370291429"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part5/InstallSharePoint_00.png?mtime=1370291429" alt="" width="271" height="270" /></a>
</p>

<span style="text-align: justify;">The next step is to attach the SharePoint image to the DVD drive of the VM and to start the prerequisite installer. This installer will install some software on the machine needed for SharePoint. It’s possible you have to run it more than once, as you may have to reboot a couple of times.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part5/InstallSharePoint_01.png?mtime=1370291434"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part5/InstallSharePoint_01.png?mtime=1370291434" alt="" width="435" height="168" /></a>
</p>

<span style="text-align: justify;">The screenshot below displays all the applications that will be installed or enabled. Some will probably be already there, so those will be skipped. Click </span>_Next_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part5/InstallSharePoint_02.png?mtime=1370291449"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part5/InstallSharePoint_02.png?mtime=1370291449" alt="" width="409" height="309" /></a>
</p>

<span style="text-align: justify;">Accept the license terms and click </span>_Next_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part5/InstallSharePoint_03.png?mtime=1370291452"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part5/InstallSharePoint_03.png?mtime=1370291452" alt="" width="407" height="306" /></a>
</p>

<span style="text-align: justify;">Wait until everything is installed… (grab a coffee, you know the drill)</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part5/InstallSharePoint_04.png?mtime=1370291453"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part5/InstallSharePoint_04.png?mtime=1370291453" alt="" width="411" height="307" /></a>
</p>

<span style="text-align: justify;">If necessary, restart your system.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part5/InstallSharePoint_05.png?mtime=1370291464"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part5/InstallSharePoint_05.png?mtime=1370291464" alt="" width="408" height="308" /></a>
</p>

<span style="text-align: justify;">Make sure you have an Internet connection, as the installer might download some software.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part5/InstallSharePoint_06.png?mtime=1370291465"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part5/InstallSharePoint_06.png?mtime=1370291465" alt="" width="385" height="307" /></a>
</p>

<span style="text-align: justify;">After a while, the setup will be completed.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part5/InstallSharePoint_07.png?mtime=1370291483"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part5/InstallSharePoint_07.png?mtime=1370291483" alt="" width="386" height="309" /></a>
</p>

<span style="text-align: justify;">Now launch the SharePoint 2013 setup and enter your product key. Click </span>_Continue_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part5/InstallSharePoint_08.png?mtime=1370291488"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part5/InstallSharePoint_08.png?mtime=1370291488" alt="" width="372" height="303" /></a>
</p>

<span style="text-align: justify;">Accept the license terms and click </span>_Continue_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part5/InstallSharePoint_09.png?mtime=1370291494"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part5/InstallSharePoint_09.png?mtime=1370291494" alt="" width="372" height="302" /></a>
</p>

<span style="text-align: justify;">Select the </span>**Complete** <span style="text-align: justify;">server type installation. You might be tempted to choose the stand-alone type, as it is intended for demo environments, but this will install SQL Server express, which we do not want. Also, if for some reason you want to add servers to your demo environment, you need the complete server type.</span>

<p style="text-align: justify;">
  If you have multiple disks, you can choose to install SharePoint on another disk, instead of on the C: drive. Click <em>Install Now</em>.
</p>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part5/InstallSharePoint_10.png?mtime=1370291497"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part5/InstallSharePoint_10.png?mtime=1370291497" alt="" width="372" height="301" /></a>
</p>

<span style="text-align: justify;">Go grab a coffee and wait while SharePoint is being installed.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part5/InstallSharePoint_11.png?mtime=1370291499"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part5/InstallSharePoint_11.png?mtime=1370291499" alt="" width="369" height="300" /></a>
</p>

<span style="text-align: justify;">Once the installation is finished, you’ll get the option to also configure it with the SharePoint configuration wizard. Since we are going to use the PowerPivot Configuration Wizard, deselect the checkbox and click </span>_Close_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part5/InstallSharePoint_13.png?mtime=1370291507"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part5/InstallSharePoint_13.png?mtime=1370291507" alt="" width="370" height="298" /></a>
</p>

<span style="text-align: justify;">That’s it, SharePoint is now installed on the virtual machine, but it is not configured yet, so it is still useless. (some might argue it’s also useless after it has been configured, but let&#8217;s not get into that)</span>

Go back to [overview][1].   
Previous part: [Installing SQL Server][2].   
Next part: [Installing PowerPivot for SharePoint][3].

 [1]: /index.php/DataMgmt/business-intelligence-1/sharepoint-2013-bi-part-1
 [2]: /index.php/DataMgmt/business-intelligence-1/sharepoint-2013-bi-part-4
 [3]: /index.php/DataMgmt/business-intelligence-1/sharepoint-2013-bi-part-6