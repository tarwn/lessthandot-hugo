---
title: Getting remote Mount Point information with PowerShell
author: Axel Achten (axel8s)
type: post
date: 2012-11-29T11:27:00+00:00
ID: 1810
excerpt: |
  In a previous post I showed how to get remote disk information with PowerShell. The script works nice untill you execute it on a server with Mount Points. When executing the following script on a server with Mount Points:
  
  Get-WmiObject win32_logicald&hellip;
url: /index.php/sysadmins/os/windows/getting-remote-mount-point-information/
views:
  - 27068
rp4wp_auto_linked:
  - 1
categories:
  - 2003 Server
  - 2008 Server
  - 2012 Server
  - Windows
tags:
  - diskspace
  - mount point
  - powershell
  - remote

---
In a previous post I showed how to [get remote disk information with PowerShell][1]. The script works nice until you execute it on a server with Mount Points. When executing the following script on a server with Mount Points:

```PowerShell
Get-WmiObject win32_logicaldisk -computer <computername> | 
select-object DeviceID, VolumeName, @{Name="Size";Expression={$_.Size/1GB}},@{Name="FreeSpace";Expression={$_.FreeSpace/1GB}},
@{Name="PCTFreeSpace";Expression={
$_.FreeSpace/$_.Size*100}}|Sort-Object -descending PCTfreespace|format-table
```

I get the following result:

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/SysAdmins/FSMP1.JPG?mtime=1354195164"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/SysAdmins/FSMP1.JPG?mtime=1354195164" width="977" height="144" /></a>
</div>

Knowing the sizes of my databases, I knew the numbers were wrong. So I opened a RDP session to the server and found this picture in Disk Management:

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/SysAdmins/FSMP2.JPG?mtime=1354195179"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/SysAdmins/FSMP2.JPG?mtime=1354195179" width="810" height="530" /></a>
</div>

At that moment I realized I wasn't getting the information from the Mount Points, just from the Disks that had a drive letter assigned to them.
  
So let's find out how we can get that information with Windows PowerShell. I still need the wmiobject but instead of the win32\_logicaldisk I'm going to use the win32\_volume. I also replace the DeviceID and VolumeName objects with Name and Label:

```PowerShell
get-wmiobject win32_volume -computer <computername|
select name, label, driveletter
```

The result looks like this:

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/SysAdmins/FSMP3.JPG?mtime=1354195191"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/SysAdmins/FSMP3.JPG?mtime=1354195191" width="733" height="200" /></a>
</div>

As you can see, the drive letter properties are empty for my Mount Points and in the name column I can find the file and folder where they are mounted.
  
So let's find the space, free space and percentage free space of the volumes. I can use the calculations from my previous script only Size needs to be replaced with Capacity:

```PowerShell
get-wmiobject win32_volume -computer <computername|
select name, label, @{Name="Capacity (GB)";Expression={$_.Capacity/1GB}},@{Name="FreeSpace (GB)";Expression={$_.FreeSpace/1GB}},
@{Name="FreeSpace (PCT)";Expression={$_.FreeSpace/$_.Capacity*100}} |
format-table
```
The result now shows the drives and Mount Points with all the requested information:

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/SysAdmins/FSMP4.JPG?mtime=1354195223"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/SysAdmins/FSMP4.JPG?mtime=1354195223" width="964" height="186" /></a>
</div>

To be able to reuse the script I do what I did in the other two PowerShell post:

```PowerShell
param(
	[string] $compname = $(Throw "Provide a Server name as first parameter")
)
Get-WmiObject win32_volume -computer $compname |
select name, label, @{Name="Capacity (GB)";Expression={$_.Capacity/1GB}},
@{Name="FreeSpace (GB)";Expression={$_.FreeSpace/1GB}},
@{Name="FreeSpace (PCT)";Expression={$_.FreeSpace/$_.Capacity*100}} |
format-table
```

End that's it, the next request for free disk space is a matter of seconds again.

 [1]: /index.php/SysAdmins/OS/Windows/getting-remote-disk-information-with