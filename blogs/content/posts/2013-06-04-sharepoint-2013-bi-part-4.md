---
title: Building a SharePoint 2013 BI Demo Environment Part 4 – Installing SQL Server
author: Koen Verbeeck
type: post
date: 2013-06-04T10:46:00+00:00
excerpt: 'In this blog post we’ll go over the installation of the SQL Server instance. This database engine will serve multiple purposes: it will host the SharePoint databases, the Reporting Services databases, the SSIS catalog and of course databases containing&hellip;'
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/sharepoint-2013-bi-part-4/
views:
  - 22608
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
  - virtual machine

---
<p style="text-align: justify;">
  In this blog post we’ll go over the installation of the SQL Server instance. This database engine will serve multiple purposes: it will host the SharePoint databases, the Reporting Services databases, the SSIS catalog and of course databases containing demo data.
</p>

<p style="text-align: justify;">
  First of all we are going to enable the .NET 3.5 framework, which is a prerequisite for SQL Server. You can find a description on how to enable this feature in this earlier blog post of mine: <a href="/index.php/DataMgmt/business-intelligence-1/error-while-enabling-netfx3">Error while enabling Windows Feature: Netfx3</a>. Normally SQL Server can enable this feature itself, but if it cannot find the necessary sources or if there isn’t an Internet connection, you can bump into the error described in the blog post.
</p>

<p style="text-align: justify;">
  Next we’ll hook the SQL Server image into the DVD drive of the virtual machine and start the SQL Server setup. The setup support rules will run, which should pass easily, unless you have for example a pending restart from Windows Update. Click <em>OK</em>.
</p>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_10.png?mtime=1369967168"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_10.png?mtime=1369967168" alt="" width="487" height="361" /></a>
</p>

<span style="text-align: justify;">You are asked to fill in a license key. If you are using Developer Edition, like me, it is already filled in. Click </span>_Next_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_11.png?mtime=1369967168"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_11.png?mtime=1369967168" alt="" width="486" height="364" /></a>
</p>

<span style="text-align: justify;">Accept the license terms (after you’ve read them all of course. But grab some coffee to keep you awake) and click </span>_Next_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_12.png?mtime=1369967168"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_12.png?mtime=1369967168" alt="" width="487" height="365" /></a>
</p>

<span style="text-align: justify;">The setup will search for product updates. Most likely you’ll get an update for Setup itself. If you have no Internet connection, you can skip this step. Click </span>_Next_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_13.png?mtime=1369967168"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_13.png?mtime=1369967168" alt="" width="486" height="362" /></a>
</p>

<span style="text-align: justify;">Setup will now download any updates and install the setup files.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_14.png?mtime=1369967168"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_14.png?mtime=1369967168" alt="" width="487" height="363" /></a>
</p>

<span style="text-align: justify;">Another set of Setup Support Rules will run. You might get a warning on the Windows Firewall, but you can ignore that. I disabled the firewalls on my virtual machine, so I pass this rule. Click </span>_Next_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_15.png?mtime=1369967168"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_15.png?mtime=1369967168" alt="" width="487" height="363" /></a>
</p>

<span style="text-align: justify;">For the Setup Role, we’ll choose </span>_SQL Server Feature Installation_<span style="text-align: justify;">. Click </span>_Next_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_16.png?mtime=1369967169"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_16.png?mtime=1369967169" alt="" width="487" height="364" /></a>
</p>

<span style="text-align: justify;">In this setup, we will install the database engine, Analysis Services, Integration Services and the client tools. Reporting Services will be installed at a later point in time. Select all the features you need and click </span>_Next_<span style="text-align: justify;">. Optionally, if you have multiple disks, you can install the shared features on another disk: change the directories at the bottom.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_17.png?mtime=1369967169"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_17.png?mtime=1369967169" alt="" width="487" height="414" /></a>
</p>

<span style="text-align: justify;">A few checks on Installation Rules will run. Normally nothing to worry about. Click </span>_Next_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_18.png?mtime=1369967169"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_18.png?mtime=1369967169" alt="" width="487" height="365" /></a>
</p>

<span style="text-align: justify;">Now we need to configure the instance. I keep everything as default, but you can change the root directory of the instance to another disk if that’s an option. Click </span>_Next_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_19.png?mtime=1369967169"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_19.png?mtime=1369967169" alt="" width="487" height="364" /></a>
</p>

<span style="text-align: justify;">You’ll get an overview of the disk space requirements. Check if everything is OK and click </span>_Next_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_20.png?mtime=1369967169"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_20.png?mtime=1369967169" alt="" width="487" height="363" /></a>
</p>

<span style="text-align: justify;">Next are the service accounts and the collations. Once again, I leave everything as default. If you are setting up a server in an actual environment (as opposed to a stand-alone demo environment) I would suggest you specify domain accounts for the service accounts. Since I’m installing a default instance, I leave the SQL Server browser disabled. Click </span>_Next_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_21.png?mtime=1369967169"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_21.png?mtime=1369967169" alt="" width="486" height="364" /></a>
</p>

<span style="text-align: justify;">The following step is configuring the database engine. I specify mixed authentication so I can define an SA account. This is some sort of back-up plan if I somehow manage to lock myself out of SQL Server. All communication should go through Windows Authentication and I will barely use SQL Server authentication. Specify a password for the SA account and most important of all, add yourself as administrator. I won’t enable filestream and I leave the data directories as default. Remark: in any other environment, I would relocate the data directories to other disks. But since this is a stand-alone demo environment (this is getting kind of tedious), I just don’t bother and install everything on the same disk. Certainly do not do this in a production environment!</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_22.png?mtime=1369967169"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_22.png?mtime=1369967169" alt="" width="487" height="364" /></a>
</p>

<span style="text-align: justify;">Next up is configuring Analysis Services. We’ll choose for the Tabular mode and add ourselves as an administrator. Once again, I leave the data directories as-is. Click </span>_Next_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_23.png?mtime=1369967169"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_23.png?mtime=1369967169" alt="" width="488" height="364" /></a>
</p>

<span style="text-align: justify;">In the next screen you can choose to send error reporting to Microsoft. I left the checkbox unchecked. Click </span>_Next_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_24.png?mtime=1369967169"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_24.png?mtime=1369967169" alt="" width="487" height="364" /></a>
</p>

<span style="text-align: justify;">A set of Installation Configuration rules will run. If every rule has passed, click </span>_Next_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_25.png?mtime=1369967169"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_25.png?mtime=1369967169" alt="" width="487" height="363" /></a>
</p>

<span style="text-align: justify;">Go through the installation overview, make sure everything is OK and click on </span>_Install_<span style="text-align: justify;">!</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_26.png?mtime=1369967169"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_26.png?mtime=1369967169" alt="" width="487" height="364" /></a>
</p>

<span style="text-align: justify;">Go grab another coffee.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_27.png?mtime=1369967170"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_27.png?mtime=1369967170" alt="" width="487" height="364" /></a>
</p>

<span style="text-align: justify;">After some time you’ll get the notification that everything has been successfully installed. Click </span>_Close_ <span style="text-align: justify;">to end the setup.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_28.png?mtime=1369967170"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_28.png?mtime=1369967170" alt="" width="487" height="364" /></a>
</p>

<span style="text-align: justify;">Since SharePoint 2013 has SQL Server 2012 SP1 as a prerequisite, we’ll install sp1 as well. You can download it </span><a style="text-align: justify;" href="http://www.microsoft.com/en-us/download/details.aspx?id=35575">here</a><span style="text-align: justify;">. Launch the setup, which will promptly verify some setup rules. Click </span>_Next_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_29.png?mtime=1369967170"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_29.png?mtime=1369967170" alt="" width="487" height="364" /></a>
</p>

<span style="text-align: justify;">Once again, accept the license terms. If you’re planning on reading them all – again – go grab some more coffee. Click </span>_Next_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_30.png?mtime=1369967257"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_30.png?mtime=1369967257" alt="" width="487" height="364" /></a>
</p>

<span style="text-align: justify;">Select the features which you want to update. That’s everything we have installed so far. Click </span>_Next_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_31.png?mtime=1369967258"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_31.png?mtime=1369967258" alt="" width="487" height="364" /></a>
</p>

<span style="text-align: justify;">A check on </span>_files in use_ <span style="text-align: justify;">will run. If something is in use, shut down the application and rerun the check. Click </span>_Next_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_32.png?mtime=1369967258"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_32.png?mtime=1369967258" alt="" width="487" height="364" /></a>
</p>

<span style="text-align: justify;">Review the overview and click </span>_Update_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_33.png?mtime=1369967258"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_33.png?mtime=1369967258" alt="" width="487" height="364" /></a>
</p>

<span style="text-align: justify;">Go to the toilet, because you’ve been drinking too much coffee…</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_34.png?mtime=1369967258"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_34.png?mtime=1369967258" alt="" width="487" height="364" /></a>
</p>

<span style="text-align: justify;">Once the update process is complete, hit </span>_Close_ <span style="text-align: justify;">to finish the setup.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_35.png?mtime=1369967258"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_35.png?mtime=1369967258" alt="" width="487" height="364" /></a>
</p>

<span style="text-align: justify;">After installing service pack 1, there might be an issue with multiple instances of Windows Installer popping up and consuming resources. This is caused by some faulty DLLs. Read all about it </span><a style="text-align: justify;" href="http://www.microsoft.com/en-us/download/details.aspx?id=36215">here</a><span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_36.png?mtime=1369967258"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_36.png?mtime=1369967258" alt="" width="529" height="127" /></a>
</p>

<span style="text-align: justify;">We’ll run KB2793634 to get rid of the issue. Launch the setup to get it going. Verify if all the setup rules have passed and click on </span>_Next._

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_37.png?mtime=1369967258"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_37.png?mtime=1369967258" alt="" width="487" height="364" /></a>
</p>

<span style="text-align: justify;">At this point, I guess you won’t read the license terms. Your loss, there’s some real beauty at the end. Click </span>_Next_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_38.png?mtime=1369967258"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_38.png?mtime=1369967258" alt="" width="487" height="364" /></a>
</p>

<span style="text-align: justify;">Select all the features you want to update and click </span>_Next_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_39.png?mtime=1369967258"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_39.png?mtime=1369967258" alt="" width="487" height="364" /></a>
</p>

<span style="text-align: justify;">The </span>_files in use_ <span style="text-align: justify;">check will run again. This time, you might get a hit on msiexec.exe. This is the Windows Installer process. You know, the one that is running rogue, which is what we’re trying to avoid with this very same update? A bit tricky, isn’t it. Go to task manager and kill the msiexec.exe process (make sure you don’t kill the update wizard) and quickly rerun the check. Click </span>_Next_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_40.png?mtime=1369967258"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_40.png?mtime=1369967258" alt="" width="487" height="364" /></a>
</p>

<span style="text-align: justify;">Glance through the overview and hit </span>_Update_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_41.png?mtime=1369967258"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_41.png?mtime=1369967258" alt="" width="487" height="364" /></a>
</p>

<span style="text-align: justify;">Wait a bit…</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_42.png?mtime=1369967258"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_42.png?mtime=1369967258" alt="" width="487" height="364" /></a>
</p>

<span style="text-align: justify;">… until there’s the notification that everything updated successfully.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_43.png?mtime=1369967259"><img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part4/InstallSQL_43.png?mtime=1369967259" alt="" width="487" height="364" /></a>
</p>

<span style="text-align: justify;">That’s it for SQL Server. You now have already a demo environment where you can show off Integration Services 2012 – which had some awesome improvements over previous editions – and SSAS Tabular. Which is also awesome of course.</span>

Go back to [overview][1].   
Previous part: [Installing the OS][2].   
Next part: [Installing SharePoint][3].

 [1]: /index.php/DataMgmt/business-intelligence-1/sharepoint-2013-bi-part-1
 [2]: /index.php/DataMgmt/business-intelligence-1/sharepoint-2013-bi-part-3
 [3]: /index.php/DataMgmt/business-intelligence-1/sharepoint-2013-bi-part-5