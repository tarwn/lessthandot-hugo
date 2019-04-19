---
title: Scan network for SQL Server instances
author: Ted Krueger (onpnt)
type: post
date: 2009-03-09T15:36:26+00:00
ID: 344
excerpt: "Securing data services is part of a DBA's critical tasks that should be performed.  This goes beyond users, roles and schemas though.  A DBA must take into account the concept of software installations in a growing and dynamic environment that often pos&hellip;"
url: /index.php/datamgmt/dbadmin/scan-network-for-sql-server-instances/
views:
  - 49191
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server Admin

---
Securing data services is part of a DBA's critical tasks that should be performed. This goes beyond users, roles and schemas though. A DBA must take into account the concept of software installations in a growing and dynamic environment that often pose serious security threats. It's very common for software to ship with the desktop version of SQL Server (MSDE2000 or SQL Express). This can be prepackaged and installed, configured and user created on the fly during installation. Most of us with developer experience know exactly how to do this and how easy it has made our jobs as developers at times. You can package up your very own free database server with your application and with a simple command and a small script, configure and have it rolling in no time. 

Given my change in careers and moving to the DBA side of things I quickly started realizing how bad this can be for the environment all together and honestly how bad some of the developers of these prepackaged solutions can really get. The age old example is the sa account usage and weak passwords. Today I did my monthly audit of the instances on my network and not all that surprising I found an unknown instance that was installed by another network administrator. The SQL Server instance was the desktop version and was prepackaged. When I asked the admin about it, he didn't even know he installed it. So why was it such an issue? The developer that packaged the MSDE used sa and left the password blank. There are several reasons why this is bad. A few of you could name right away is. Gaining access to that database servers databases and having unrestricted permissions to them. The one I worry most about is linked servers and other data source connections you can make while in there. With a prepackaged MSDE installed under the radar, you can gain access to anything and everything.

After saying all that I wanted to post how I audit the network for instances and maintain the knowledge of what I have out there and what may be potentially a problem.

First I use SQL Ping which can be found http://www.sqlsecurity.com/Tools/FreeTools/tabid/65/Default.aspx

SQL Ping isn't a secret and most of you probably know or have used it. It seems to be the easiest and free instance discovery tool out there. There is a good article over on SQL Server Central that goes over the usage @ http://www.sqlservercentral.com/articles/64016/

In most cases your will know you facilities IP schema so it makes searches fairly simple. Example would be if you have your corporate location on 192.168.61.xx and your primary manufacturing location on 192.168.71.xx. For SQL Ping all we need to do is the following then to search those networks for existing SQL Server instances.

Sqlping3cl.exe –scantype range –startIP 192.168.61.0 –endIP 192.168.61.254 –output instance\_searchcorporate\_location.csv

This outputs a file listing all found instances. The primary data I'm worried about for the task of security audits are the columns ServerIP, ServerName, InstanceName, TrueVersion and Details.

This data gives me plenty to compare older audits with this one and let me know what may be a potential problem or a new instance that comes up without the DBA being notified. 

None of this is useful if you ask me though unless you can automate it. Seeing as SQL Ping is a command line utility your options are wide. You could use windows task scheduler to run the utility, or you can keep the entire process on the instance by scheduling it through a job.

The SSIS package is quite large on my job server due to error handling and script steps to validate files, SQL Ping functionality and a few other things that makes it lengthy. In a real situation you're also going to want to make it dynamic. Pass variables in the SSIS around to call up SQL Ping to search difference schemas, instances and file locations. You can also use the argument IPList to specify the IPs to search. Make sure firewalls aren't getting in your way by running SQL Ping first from the command prompt initially.
  
For an example, I'm going to create a new package to run off a small subset of IP's and static paths.
  
First task bring over two send email tasks which will act as our success and failure notifications. Add the SMTP connection to the connection manager and go ahead and configure the tasks to point to your local or remote SMTP server.
  
Second task, bring over two script tasks. First will be named “Call up SQLPing”, and the second, “Validate SQLPing Run and file output”.
  
Go into the designer of the first script task and add IO and Diagnostics in so we can access the files and also call a new process. I created a folder on my root c drive as C:instance_search where the files for this example will be written. 

The code will look like such

```vbnet
Public Sub Main()
        If File.Exists("C:instance_searchcorporate_location.csv") Then
            File.Delete("C:instance_searchcorporate_location.csv")
        End If

        Dim proc As New Process
        Dim args As New ProcessStartInfo()

        args.FileName = "c:sqlping3cl.exe"
        args.Arguments = "-scantype range -startIP 192.168.xx.xx -EndIP 192.168.xx.xx -Output instance_searchcorporate_location.csv"

        proc.StartInfo = args
        proc.Start()
        proc.WaitForExit()

        Dts.TaskResult = Dts.Results.Success
    End Sub
```
Replace the xx with your own structure. There are some things I need to point out with SQLPing 3.0 command line. I have problems putting paths in such as C:folderfile.csv. I have a posting up on sqlsecurity.com and Chip has made some replies. Seeing as the following command works fine I haven't pulled SQL Ping 3.0 from this process. The new version is still a very good tool that based all my tests for stability. I thank Chip again for the hard work and offering of SQL Ping to the community. If you have problems you can always comment here as well for help or go to the sqlsecurity.com community.

This is the working command for directory paths other than the root:

c: sqlping3cl.exe “-scantype range -startIP 192.168.xx.xx -EndIP 192.168.xx.xx -Output instance\_searchcorporate\_location.csv

Next step in the SSIS package is to edit the second script task. This step will validate the file creation by SQL Ping and base the flow for the next step on the existence of the file. 

The code will be as follows

```vbnet
Public Class ScriptMain

	Public Sub Main()
        If File.Exists("C:instance_searchcorporate_location.csv") Then
            Dts.TaskResult = Dts.Results.Success
        Else
            Dts.TaskResult = Dts.Results.Failure
        End If
	End Sub

End Class
```
All we need to accomplish here is to validate the files existence. If the file does not exist then our steps will go to the send failure email task and notify you.
  
Next step, drag over a Data Flow task. Add to the connection managers a flat file connection to the csv file in the instance\_search folder as a flat file connection. Second, add an ADO.NET or OLEDB connection to your instance. In my case I have a DBA database on the instance that this package is created. In this database I created a table instance\_audit. This table is where we pump the results from SQL Ping in order to report on later. The create table would be...

sql
CREATE TABLE [dbo].[instance_audit](
	[ServerIP] [varchar](50) NULL,
	[TCPPort] [varchar](5) NULL,
	[ServerName] [varchar](55) NULL,
	[InstanceName] [varchar](55) NULL,
	[BaseVersion] [char](20) NULL,
	[SSNetlibVersion] [varchar](50) NULL,
	[TrueVersion] [varchar](50) NULL,
	[ServiceAccount] [varchar](55) NULL,
	[IsClustered] [varchar](25) NULL,
	[Details] [varchar](max) NULL,
	[DetectionMethod] [varchar](55) NULL
) ON [PRIMARY]
```
In the data flow tab drag over a flat file source, data conversion and OLEDB destination.
  
Configure the flat file source with the flat file connection. Verify the mappings all look good after configuring the source. Connect to the flat file source to the data conversion and open up the configuration window of the conversion. I edit my types in the conversion so they appear as

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//mapp_view.gif" alt="" title="" />
</div>

This ensures they will pump to SQL accurately and without issues. Connect the conversion to the OLEDB destination and open the destination editor. Select the SQL connection you created earlier and then in the table listing find your table. Verify in mappings then that your converted values are mapped. By default you will probably get the original columns. I typically check “Keep nulls” for these types of tasks also. Sense we rely on external processes that are out of our control the opportunity for null values is more probable. 

Go back the control flow tab and connect all your steps to the email tasks so notifications go out on success and failures. You should end up with something similar to 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//pack_view.gif" alt="" title="" />
</div>

Running this package fills the discovered instances into the instance_audit table for later reporting in SSRS or you can send out a notification with a query attachment for review.
  
Such as...

sql
EXEC msdb.dbo.sp_send_dbmail @recipients='dba@your_company.com',
	@subject = 'Instance discovery completed.',
	@body = 'Review attachment for results of SQL Server scan of the network',
	@body_format = 'HTML',
	@profile_name = 'DBA',
	@query = 'Select * From db.dbo.instance_audit',
	 @query_attachment_filename = 'Instance_Discovery.csv',
	 @query_result_header = false,
	 @query_result_width = 10000,
	 @attach_query_result_as_file = 1,
	 @query_result_separator = ','
```
It takes around 1 minute 30 seconds to scan 50 IPs, finding 15 instances. The longest time that I have found is the SQL Ping and writing the output contents. That is a local test on my personal machine. The server scans a complete 0 – 254 (24 instances) and completes the insert into the table in around a minute.