---
title: 'My Favorite PowerShell Script: Reading Log Files'
author: Jes Borland
type: post
date: 2011-10-28T14:41:00+00:00
ID: 1363
excerpt: I'm in love. With a PowerShell script I wrote.
url: /index.php/datamgmt/dbadmin/my-favorite-powershell-script-reading/
views:
  - 8549
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server Admin

---
I'm in love. With a PowerShell script I wrote. 

We have multiple SQL Server Agent jobs that run on a daily or weekly basis. These jobs write logs to a folder on the server. Example: D:mssqlserverbackup

When a job fails, we receive a ticket for it. The ticket does not include the reason for the failure. The existing procedure to find the error and resolve it was to RDP to the server, navigate to the directory, and read the text file. 

I knew there had to be a better way. Once I learned enough about PowerShell to be dangerous, I put together this script. 

> Disclaimer: I know it's not perfect. Yes, there are probably improvements I could make. It's a giant step forward for me.

```powershell
#Go to a server share, read log 
#server
$server='GRRLGEEK2008R2'
#instance - if default, enter mssqlserver
$instance='mssqlserver'
#job
$job='backup'
#build share path
$path='\' + $Server + '' + $instance + '$logs' + $job
#$path
#go to server
Get-ChildItem -Path $Path -Filter *log | Sort-Object -Descending LastWriteTime | Select -First 1 -Property LastWriteTime 
Get-ChildItem -Path $Path -Filter *log | Sort-Object -Descending LastWriteTime | Select -First 1 | Get-Content
```

Breaking it down: 

$server, $instance, $job, and $path are the variables. 

Get-ChildItem –Path will look for all the objects in the current directory, which is the $path variable. 

-Filter will narrow those results down to only the files with "log" in the name. 

That output is sent to the Sort-Object –Descending LastWriteTime command, which is sorting the files, in descending order, by the last write date. I only want to view the latest file. 

Here, the two commands split. 

The first command sends output to the Select – First command, which pulls the latest file. –Property LastWriteTime will display the time the log was written. This is helpful to me so I can see when my job started. 

The second command sends output to the Get-Content command, which displays the text file in my ISE window. I can then review it for errors. 

It used to take about 3 minutes to determine the cause of the error. It now takes about 30 seconds. My team averages about 20 tickets per day that this can be used on. Over the course of a year, this saves my team approximately 600 hours of labor.

Six hundred hours. 

If you haven't learned PowerShell yet, now is the time.