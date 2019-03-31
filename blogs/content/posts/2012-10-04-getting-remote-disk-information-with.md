---
title: Getting remote disk information with Windows PowerShell
author: Axel Achten (axel8s)
type: post
date: 2012-10-04T11:44:00+00:00
excerpt: 'A couple of weeks ago someone asked me why they should upgrade to MS SQL Server 2012. I named a bunch of reasons and only remembered afterwards that the possibility to use Windows Server Core could also be a surplus. Just 2 days later this Case for Core&hellip;'
url: /index.php/sysadmins/os/windows/getting-remote-disk-information-with/
views:
  - 25486
rp4wp_auto_linked:
  - 1
categories:
  - Windows
tags:
  - disk usage
  - powershell
  - remote admin
  - wmi

---
A couple of weeks ago someone asked me why they should upgrade to MS SQL Server 2012. I named a bunch of reasons and only remembered afterwards that the possibility to use Windows Server Core could also be a surplus. Just 2 days later this [Case for Core][1] post from Jeremiah Peschka ([blog][2] | [twitter][3]) made me realize there was a lot of work for me before I could say that running MS SQL Server 2012 on Windows Server Core. I already read the book [Microsoft SQL Server 2008 Administration with Windows PowerShell][4] and reviewed it [here][5].So I decided to try to avoid the usage of Remote Desktop as much as possible and finally start to use PowerShell on a daily basis. One of the first things I needed to do was the restore of a database on another server. So before I could start the restore I needed to check the servers&#8217; disk configuration and available space. So let&#8217;s see how to do this with PowerShell.
  
To get information about disks we use the win32_logicaldisk wmiobject and with the -computer parameter we specify our server name:

<pre>GET-WmiObject win32_logicaldisk -computer &lt;computername&gt;</pre>

The result looks like this:

<div class="image_block">
  <a href="/wp-content/uploads/users/axel8s/PSDiskInfo1.PNG?mtime=1349357595"><img alt="" src="/wp-content/uploads/users/axel8s/PSDiskInfo1.PNG?mtime=1349357595" width="232" height="536" /></a>
</div>

As you can see, the requested information is there and if I only need to do this just once it&#8217;s fine but I&#8217;m going to need this information several times on different servers so let’s make the result more user friendly.
  
First things first, let&#8217;s just select the data I need, with a pipe I pass the input from the Get-WmiObject to the second part of my command and there I use the Select-Object to specify the object that I want:

<pre>Get-WmiObject win32_logicaldisk -computer &lt;computername&gt; | select-object DeviceID, VolumeName,Size,FreeSpace</pre>

This looks better:

<div class="image_block">
  <a href="/wp-content/uploads/users/axel8s/PSDiskInfo2.png?mtime=1349357671"><img alt="" src="/wp-content/uploads/users/axel8s/PSDiskInfo2.png?mtime=1349357671" width="964" height="110" /></a>
</div>

Next step is to see the percentage free space. Using @{Name=&#8221;&#8221;,Expression={}}:

<pre>Get-WmiObject win32_logicaldisk -computer &lt;computername&gt; | select-object DeviceID, VolumeName,Size,FreeSpace,@{Name="PCTFreeSpace";Expression={$_.FreeSpace/$_.Size*100}}</pre>

The code works but now I have to scroll again to see all the disk info:

<div class="image_block">
  <a href="/wp-content/uploads/users/axel8s/PSDiskInfo3.png?mtime=1349357695"><img alt="" src="/wp-content/uploads/users/axel8s/PSDiskInfo3.png?mtime=1349357695" width="275" height="458" /></a>
</div>

I add a second pipe and specify that I want my result formatted as a table, I also make sure the size and free space make more sense:

<pre>Get-WmiObject win32_logicaldisk -computer &lt;computername&gt; | 
select-object DeviceID, VolumeName, @{Name="Size";Expression={$_.Size/1GB}},@{Name="FreeSpace";Expression={$_.FreeSpace/1GB}},
@{Name="PCTFreeSpace";Expression={
$_.FreeSpace/$_.Size*100}}|format-table</pre>

Better no?

<div class="image_block">
  <a href="/wp-content/uploads/users/axel8s/PSDiskInfo4.png?mtime=1349357709"><img alt="" src="/wp-content/uploads/users/axel8s/PSDiskInfo4.png?mtime=1349357709" width="964" height="141" /></a>
</div>

Now you can save this script to a text file, rename the file extension to .ps1 and edit the file every time you need to query another server. Or you can use a parameter. I named my file GetDiskUsage.ps1 and started the script with param([Datatype] $Variablename):

<pre>param(
	[string] $compname )
Get-WmiObject win32_logicaldisk -computer &lt;computername&gt; | 
select-object DeviceID, VolumeName, @{Name="Size";Expression={$_.Size/1GB}},@{Name="FreeSpace";Expression={$_.FreeSpace/1GB}},
@{Name="PCTFreeSpace";Expression={
$_.FreeSpace/$_.Size*100}}|Sort-Object -descending PCTfreespace|format-table</pre>

Now I can execute the script with the following command:

<pre>.Getdiskusage &lt;Computername&gt;</pre>

Notice that I also added a Sort-Object before the format to be able to see what disk has the most available free space first:

<div class="image_block">
  <a href="/wp-content/uploads/users/axel8s/PSDiskInfo5.png?mtime=1349357719"><img alt="" src="/wp-content/uploads/users/axel8s/PSDiskInfo5.png?mtime=1349357719" width="964" height="103" /></a>
</div>

To finish things up a use a Throw in my parameter definition to avoid an ugly error message when I execute the script without specifying a parameter value:

<pre>param(
	[string] $compname = $(Throw "Provide a Server name as first parameter") )
Get-WmiObject win32_logicaldisk -computer &lt;computername&gt; | 
select-object DeviceID, VolumeName, @{Name="Size";Expression={$_.Size/1GB}},@{Name="FreeSpace";Expression={$_.FreeSpace/1GB}},
@{Name="PCTFreeSpace";Expression={
$_.FreeSpace/$_.Size*100}}|Sort-Object -descending PCTfreespace|format-table</pre>

When I now execute the script without specifying a computer name:

<pre>.Getdiskusage</pre>

I see I have to specify a computer name:

<div class="image_block">
  <a href="/wp-content/uploads/users/axel8s/PSDiskInfo6.png?mtime=1349357734"><img alt="" src="/wp-content/uploads/users/axel8s/PSDiskInfo6.png?mtime=1349357734" width="953" height="72" /></a>
</div>

I know there are alternatives to write this wmi query. This is just a way that works for me and does the trick now. Feel free to comment and/or correct me if there are better solutions.

 [1]: http://www.brentozar.com/archive/2012/09/case-for-core/
 [2]: http://www.brentozar.com/archive/author/jeremiah-peschka/
 [3]: https://twitter.com/peschkaj
 [4]: http://www.wrox.com/WileyCDA/WroxTitle/Microsoft-SQL-Server-2008-Administration-with-Windows-PowerShell.productCd-0470477288.html
 [5]: http://axel8s.wordpress.com/2012/01/20/book-review-microsoft-sql-server-2008-administration-with-windows-powershell/