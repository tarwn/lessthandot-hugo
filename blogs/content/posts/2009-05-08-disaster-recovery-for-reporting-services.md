---
title: Disaster/Recovery for Reporting Services objects (rdl, rds etc..)
author: Ted Krueger (onpnt)
type: post
date: 2009-05-08T14:47:37+00:00
ID: 418
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/disaster-recovery-for-reporting-services/
views:
  - 16312
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server Admin

---
One of the must have tools for any DBA that has one or multiple SSRS instances of SSRS running, is the RSScripter tool written by Jasper Smith. I can't say enough about how critical this tool has been to my success in administrating and successfully providing a 100% report uptime goal. There are several key tasks this tool accomplishes for me without much more time consuming scripting projects. Without them it would be a painstaking task to accomplish backing up SSRS and shipping offsite.

Today I thought I would share my DR setup for an SSRS instance primarily revolving around the reports. 

Below is a reference key that you will need in order to accomplish this task. The download links are all there.

RS utility http://msdn.microsoft.com/en-us/library/ms162839(SQL.90).aspx
  
Samples http://www.codeplex.com/SqlServerSamples/Release/ProjectReleases.aspx?ReleaseId=4000
  
rsscripter http://www.sqldbatips.com/showarticle.asp?ID=62

I put the SQL Server Samples link because this is a must resource so you can get your feet wet with rss scripting and in case you do not have a development SSRS instance, you can use a local instance and the sample scripts and reports.

The basic setup and flow of all of this is done in a simple batch file. Yes, we still use .bat files. Even vbs sometimes 😉

Step 1) Script all the needed SSRS objects to the file system using RSScripter.
  
Step 2) Using RSS and the RS utility, publish everything to an offsite SSRS instance. You will see later that this is all done for you but can be written on your own. The tasks are big but very simple to get accomplished. 

The first step is calling RSScripter. Given the config files this is all automated nicely. 

First I would test out RS and publishing to a report server. The RS.EXE is located in [installation folder]90ToolsBinn

Open cmd and change directory so you are in context to run the RS utility.

My recommendation is to run the samples rss provided for publishing. This will give you a place to troubleshoot security or any other issues before hand. This will take out your coding of the rss as a possible cause for failures if you do some other coding. It will also troubleshooting the RSScripter cmd creations before you start creating and trying to send hundreds of objects down the pipe.

Once you know RS is publishing ok don't forget to remove the test off the instance. In the RSScripter readme file there is a provided sample of automating the scripting and publishing tasks. This sample is pretty well formed and can really be used out of the box with minor changes. The author has even provided the RSS code to script out your roles. Basically not only has the work been done in scripting the reports and such, but the work for automating everything. I'm not going to go into the permmision sets and such in this blog but hope to get a follow up quickly on that task. Security in my DR solution is maintained differently than the primary sources of course. This means I change things a bit so I don't want my report roles and such sent down. Let's just get out objects over there for now so you have a recovery point.

First command is to actually script everything. This can be accomplished with the RSScripterCMD which is included with the RSScripter.exe (GUI version)

Here is how a sample command would look
  
c:tempreportsrsscriptercmd /s:http://localhost/ReportServer/ReportService2005.asmx /c:"c:tempreportsRSScripter.cfg" /l:"c:tempreportsrsscripterlog.txt"

You're Catalog.xml file should look like this

```xml
<?xml version="1.0" ?>
<RSCatalog>
	<CatalogItems>
		<CatalogItem Path="/" Recursive="True" />
  </CatalogItems>
	<Roles />
	<Schedules />
</RSCatalog>
```
Also you can replace the URL to the instance with editing the Server.xml like this and then call it as /S:SQL2005

```xml
<?xml version="1.0" encoding="utf-8"?>
<servers>
	<server label="SQL2005" reportservice="http://localhost/ReportServer/ReportService2005.asmx" />
</servers>
```
If you run this after placing all your required files into the tempreports folder, you will get everything off the local instance scripted to the tempreports folder. The other thing that is scripted is a bat file already preformatted to reload all the objects to the report server you just backed up. This is really nice for quick recovery of the local instance. The other thing this is nice for is seeing a perfect example of how to remote deploy the backup.

Going into this batch file you will see the initial key variables for execution

```xml
@echo off
:: ** Script generated by Reporting Services Scripter Cmd 2.0.0.16 **
:: ** Created by Jasper Smith (jas@sqldbatips.com)                 **
:: ** See http://www.sqldbatips.com for help/support               **

::Script Variables
SET LOGFILE="RS Scripter Load Log.txt"
SET SCRIPTLOCATION=
SET BACKUPLOCATION=
SET REPORTSERVER=http://LocalHost/ReportServer
SET RS="C:Program FilesMicrosoft SQL Server90ToolsBinnRS.EXE"
SET TIMEOUT=60

::Clear Log file
IF EXIST %logfile% DEL %logfile%

::Write Log Header
ECHO Reporting Services Scripter Load Log 2.0.0.16 >>%LOGFILE%
ECHO. >>%LOGFILE%
ECHO Starting Load at %DATE% %TIME% >>%LOGFILE%
ECHO SCRIPTLOCATION = %SCRIPTLOCATION% >>%LOGFILE%
ECHO REPORTSERVER   = %REPORTSERVER% >>%LOGFILE%
ECHO BACKUPLOCATION = %BACKUPLOCATION% >>%LOGFILE%
ECHO SCRIPTLEVEL    = SQL2005 >>%LOGFILE%
ECHO TIMEOUT        = %TIMEOUT% >>%LOGFILE%
ECHO RS             = %RS% >>%LOGFILE%
ECHO. >>%LOGFILE%
```
Really the only thing you would need to do is change the REPORTSERVER variable to the remote instance and run this batch file. It will deploy based on the RSS scripts in each directory the objects in the order they are required. This is typically, Folder, Data Source and Report at a minimum. I'm not all that great with recursive loops through directories in batch so I wrote a VBScript script to handle the replace. Why not really? The file is created already and we can simply read the contents in, replace the path and then run the cmd. If you want to write some other method to simplify the number of objects being called, then go for it. This to me just seemed as simple sense the work has already been done.

Here is the code for AlterDestSRV.vbs

```VB
Const ForWriting = 2
Const ForReading = 1

file = WScript.Arguments.Item(0)
server = WScript.Arguments.Item(1)
serverDest = WScript.Arguments.Item(2)

Set objFSO = CreateObject("Scripting.FileSystemObject")
Set objTextFile = objFSO.OpenTextFile(file , ForReading, True)

strText = objTextFile.ReadAll
strText = Replace(strText,"http://" &amp; server &amp; "/ReportServer","http://" &amp; serverDest &amp; "/ReportServer")

objTextFile.Close
Set objTextFile = objFSO.OpenTextFile(file , ForWriting, True)
objTextFile.WriteLine strText
objTextFile.Close
```
So now my primary call looks like this

```
C:tempreportsrsscriptercmd /s:http://localhost/ReportServer/ReportService2005.asmx /c:"c:tempreportsRSScripter.cfg" /l:"c:tempreportsrsscripterlog.txt"
Call c:tempreportsAlterDestSRV.vbs "C:tempreportsrs scripter load all items.cmd" "localhost" "remotehost"
```
The very last thing is to call the cmd. Just add the call to the bat file so the total contents are

```
C:tempreportsrsscriptercmd /s:http://localhost/ReportServer/ReportService2005.asmx /c:"c:tempreportsRSScripter.cfg" /l:"c:tempreportsrsscripterlog.txt"
Call c:tempreportsAlterDestSRV.vbs "C:tempreportsrs scripter load all items.cmd" "localhost" "remotehost"
C:tempreportsRS Scripter Load All Items.cmd
```
Depending on the dynamic nature of your reporting instances you may want to schedule these to run daily, weekly or monthly. Some things to think about and consider in your DR solution for SSRS are, do you want to overwrite objects for each iteration of the replication and also, how should you handle the data sources in general. Typically if you fail over to your hot site, you had a major failure on the primary. This means your data sources are probably not going to be up anymore. In my SSRS instances, data sources are controlled completely by the DBA (me). I hand out the DS's to the developers and do not allow overwriting at all. Sense this is well maintained, I create the data sources on both sites when the need is there for a new one. You can alter the data sources with RSS though. I will show you how to do this in another blog on RSS in general. You may get all you need just from RSScripter's readme though. It was put together very well.

The last step is to throw the calls into a SQL Agent job with a Operating System type step. Then schedule away.

As always I'm open to new tools and better methods so please comment here and let us all know of them.