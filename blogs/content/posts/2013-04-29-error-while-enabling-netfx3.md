---
title: 'Error while enabling Windows Feature: Netfx3'
author: Koen Verbeeck
type: post
date: 2013-04-29T07:43:00+00:00
ID: 2073
excerpt: "Recently I was setting up a virtual machine environment to do some demo's. One of the virtual machines would be the home for a SQL Server database engine supporting a SharePoint 2013 farm. Since it is a back-end database, the server was not connected to&hellip;"
url: /index.php/webdev/business-intelligence/error-while-enabling-netfx3/
views:
  - 25036
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
tags:
  - .net 3.5
  - error while enabling windows feature netfx3
  - sql server set-up
  - syndicated

---
<p style="text-align: justify;">
  Recently I was setting up a virtual machine environment to do some demo's. One of the virtual machines would be the home for a SQL Server database engine supporting a SharePoint 2013 farm. Since it is a back-end database, the server was not connected to the Internet. Instead, it was connected to the other servers through the Hyper-V internal network.
</p>

<p style="text-align: justify;">
  During the installation of SQL Server on this machine, I got the following error:
</p>

<p style="text-align: justify;">
  <em>Error while enabling Windows Feature: Netfx3</em>
</p>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/EnableNetfx3/error.png?mtime=1366956522"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/EnableNetfx3/error.png?mtime=1366956522" alt="" width="702" height="303" /></a>
</p>

<span style="text-align: justify;">A quick search on the Web taught me this is the activation of .NET 3.5. I knew this was a prerequisite for SQL Server. In the past you had to enable this before you started the SQL Server set-up, but nowadays the set-up does this for you. Aren't we spoiled? Also keep in mind enabling .NET 4.5 isn't enough: the 3.5 version has to be explicitly enabled.</span>

<p style="text-align: justify;">
  Why did it crash now and not in all my previous installations of SQL Server? It seemed the SQL Server set-up could locate the sources to install .NET 3.5. Normally it would try to download them, but since the machine was not connected to the Internet this failed as well, resulting in the error.
</p>

<p style="text-align: justify;">
  Luckily it is very easy to enable .NET 3.5 using the GUI. In your Server Manager, click on <em>Add roles and features.</em>
</p>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/EnableNetfx3/InstallSQL_01.png?mtime=1366956829"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/EnableNetfx3/InstallSQL_01.png?mtime=1366956829" alt="" width="353" height="226" /></a>
</p>

<span style="text-align: justify;">Choose for </span>_Role-based or feature-based installation_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/EnableNetfx3/InstallSQL_02.png?mtime=1366957070"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/EnableNetfx3/InstallSQL_02.png?mtime=1366957070" alt="" width="556" height="390" /></a>
</p>

<span style="text-align: justify;">Choose the correct server, skip the Server Roles and click </span>_Next_ <span style="text-align: justify;">until you're on the Features page. Select </span>_.NET Framework 3.5 Features_ <span style="text-align: justify;">and click </span>_Next_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/EnableNetfx3/InstallSQL_05.png?mtime=1366957100"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/EnableNetfx3/InstallSQL_05.png?mtime=1366957100" alt="" width="553" height="392" /></a>
</p>

<span style="text-align: justify;">On the installation page you'll be confronted with a warning that the source files are missing. Fortunately you can specify an alternate source path at the bottom.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/EnableNetfx3/InstallSQL_06.png?mtime=1366957131"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/EnableNetfx3/InstallSQL_06.png?mtime=1366957131" alt="" width="553" height="390" /></a>
</p>

<span style="text-align: justify;">Click on the link and specify the path to the </span>_sourcessxs_ <span style="text-align: justify;">folder on your Windows Server 2012 media. In my case this was on the D: drive, which is the DVD drive.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/EnableNetfx3/InstallSQL_07.png?mtime=1366957139"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/EnableNetfx3/InstallSQL_07.png?mtime=1366957139" alt="" width="449" height="351" /></a>
</p>

<span style="text-align: justify;">Hit </span>_OK_ <span style="text-align: justify;">and on the Installation page, click </span>_Install_<span style="text-align: justify;">. The .NET framework 3.5 will now be installed.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/EnableNetfx3/InstallSQL_09.png?mtime=1366957167"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/EnableNetfx3/InstallSQL_09.png?mtime=1366957167" alt="" width="556" height="392" /></a>
</p>

<span style="text-align: justify;">After the set-up, you can now re-launch the SQL Server set-up!</span>

<p style="text-align: justify;">
  For the command line geeks amongst us, read the following <a href="http://garvis.ca/2013/01/04/installing-netfx3-on-windows-server-2012/">blog post</a> on how to enable .NET 3.5 using <a href="http://msdn.microsoft.com/en-us/library/windows/desktop/dd371719(v=vs.85).aspx">dism</a>.
</p>