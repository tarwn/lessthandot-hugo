---
title: Database Projects – Installing the Database Project Template
author: Axel Achten (axel8s)
type: post
date: 2013-01-08T12:10:00+00:00
ID: 1905
excerpt: |
  This is the second post in the Database Projects series. The first post was Setting up Source Control.
  So now we have Source Control in place we can start a database project. To do this launch SQL Server Data Tools 2012 (SSDT). In the Start Page, choos&hellip;
url: /index.php/datamgmt/dbprogramming/database-projects-installing-the-database-project-template/
views:
  - 25091
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - database lifecycle
  - database projects
  - sql server datatools
  - ssdt

---
This is the second post in the Database Projects series. The first post was [Setting up Source Control][1].
  
So now we have Source Control in place we need to install the Database Projects Template. To do this launch SQL Server Data Tools 2012 (SSDT). In the Start Page, choose for a New Project:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/DBPCreate1.png?mtime=1357652727"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/DBPCreate1.png?mtime=1357652727" width="150" height="150" /></a>
</div>

When you want to create a Database Project for the first time, you&#8217;ll see a Web Install in the New Project Template screen when you select SQL Server:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/DBPCreate2.png?mtime=1357652946"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/DBPCreate2.png?mtime=1357652946" width="450" height="300" /></a>
</div>

When you click the Web Install, a new pop-up screen will appear telling you you have to install Microsoft SQL Server Data Tools. Off course this is what you want so hit install:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/DBPCreate3.png?mtime=1357652946"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/DBPCreate3.png?mtime=1357652946" width="200" height="100" /></a>
</div>

A web page will open where you can download SQL Server Data Tools for Visual Studio 2010 or for Visual Studio 2012. Since I&#8217;m using the SQL Server Data Tools delivered with SQL Server 2012 and this is a Visual Studio 2010 Shell I will download the Visual Studio 2010 version:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/DBPCreate4.png?mtime=1357652946"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/DBPCreate4.png?mtime=1357652946" width="400" height="200" /></a>
</div>

Another screen will show, giving you more details about what&#8217;s going to happen and give you more options if you need to install SQL Server Data Tools on a machine with no internet access or with other language needs. Since Dutch is not supported and my machine has internet access I just start the download:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/DBPCreate5.png?mtime=1357652946"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/DBPCreate5.png?mtime=1357652946" width="200" height="300" /></a>
</div>

Now you can choose to Save or Run the setup. I&#8217;m just going to run it:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/DBPCreate6.png?mtime=1357652946"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/DBPCreate6.png?mtime=1357652946" width="200" height="100" /></a>
</div>

Don&#8217;t know if you can trust Microsoft Corporation as a Publisher but hitting Don&#8217;t Run will not help you on getting the software installed. So hit Run:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/DBPCreate7.png?mtime=1357652946"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/DBPCreate7.png?mtime=1357652946" width="200" height="100" /></a>
</div>

Now we need to accept the License Terms to install:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/DBPCreate8.png?mtime=1357652946"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/DBPCreate8.png?mtime=1357652946" width="300" height="200" /></a>
</div>

Now this is why you have to admire sysadmins, allow the software to make changes to your computer:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/DBPCreate9.png?mtime=1357652946"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/DBPCreate9.png?mtime=1357652946" width="200" height="100" /></a>
</div>

Finally you can take a nap while the download runs and the installer starts installing.
  
After the Setup finish you can Close the installer:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/DBPCreate10.png?mtime=1357652947"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/DBPCreate10.png?mtime=1357652947" width="300" height="200" /></a>
</div>

Close SQL Server Data Tools and start it again. Because of the new version you might get the question what Default Environment Settings you want. I chose SQL Server Development Settings before starting Visual Studio.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/DBPCreate11.png?mtime=1357652947"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/DBPCreate11.png?mtime=1357652947" width="200" height="200" /></a>
</div>

.
  
I still don&#8217;t understand why the SQL Server Database Project Template isn&#8217;t delivered with the initial setup of the SQL Server Data Tools but we&#8217;re almost ready to create our first project.

 [1]: /index.php/DataMgmt/DBAdmin/database-projects-setting-up-source