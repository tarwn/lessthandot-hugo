---
title: Getting remote SQL Service information with Windows Powershell
author: Axel Achten (axel8s)
type: post
date: 2012-10-11T07:37:00+00:00
ID: 1751
excerpt: |
  Every DBA managing multiple SQl Servers with multiple instances will know the issues with developpers, project managers and others that don't know the importance of the instancename when they request you to take some action.
  So you can start some e-mai&hellip;
url: /index.php/sysadmins/os/windows/getting-remote-sql-service-information/
views:
  - 10781
rp4wp_auto_linked:
  - 1
categories:
  - Windows
tags:
  - powershell
  - service
  - sql server

---
Every DBA managing multiple SQL Servers with multiple instances will know the issues with developers, project managers and others that don't know the importance of the instance name when they request you to take some action.
  
So you can start some e-mail ping pong to get the instance name, open the server documentation or RDP to the server to find the installed instances. But in the time you would need to do this you can write yourself a PowerShell script to get the remote service information.
  
First step getting remote services information:

```PowerShell
Get-Service -ComputerName <SQLServerHostName>
```

This gives you all the information you need:

<div class="image_block">
  <a href="/wp-content/uploads/users/axel8s/PSSQLService1.PNG?mtime=1349948019"><img alt="" src="/wp-content/uploads/users/axel8s/PSSQLService1.PNG?mtime=1349948019" width="646" height="278" /></a>
</div>

But since I only need SQL Server information I will filter out all the other services using the Where-Object and the like operator:

```PowerShell
Get-Service -ComputerName <SQLServerHostName> -name "MSSQL*"
```

As you can see we can now see the installed instances of SQL Server on our remote server. Only if the Instance name is too long you will see … at some point:

<div class="image_block">
  <a href="/wp-content/uploads/users/axel8s/PSSQLService2.PNG?mtime=1349948026"><img alt="" src="/wp-content/uploads/users/axel8s/PSSQLService2.PNG?mtime=1349948026" width="575" height="138" /></a>
</div>

Since we only need the Name and ass a surplus the Status of our SQL Server Services we can format the output:

```PowerSHell
Get-Service -ComputerName <SQLServerHostName> -name "MSSQL*"|Format-Table -Property Name, Status
```

And now we have only the information we need:

<div class="image_block">
  <a href="/wp-content/uploads/users/axel8s/PSSQLService3.PNG?mtime=1349948035"><img alt="" src="/wp-content/uploads/users/axel8s/PSSQLService3.PNG?mtime=1349948035" width="983" height="136" /></a>
</div>

Like in my [previous post][1] I will put the command in a PS1 script using a parameter and a Throw to be able to reuse the script:

```PowerShell
param(
	[string] $compname = $(Throw "Provide a SQL Server name as first parameter")
)
Get-Service -ComputerName $compname -name "MSSQL*"|Format-Table -Property Name, Status, DisplayName
```
Executing the script will look like this:

<div class="image_block">
  <a href="/wp-content/uploads/users/axel8s/PSSQLService4.PNG?mtime=1349948043"><img alt="" src="/wp-content/uploads/users/axel8s/PSSQLService4.PNG?mtime=1349948043" width="983" height="253" /></a>
</div>

Et voila, another point I can take of my list of items I can't do on a Server Core…

 [1]: /index.php/SysAdmins/OS/Windows/getting-remote-disk-information-with