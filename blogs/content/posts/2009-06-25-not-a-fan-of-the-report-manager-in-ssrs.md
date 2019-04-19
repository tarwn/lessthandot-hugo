---
title: 'Not a fan of the Report Manager in SSRS?  Using SSRS procedures to get the job done'
author: Ted Krueger (onpnt)
type: post
date: 2009-06-25T15:02:01+00:00
ID: 481
url: /index.php/datamgmt/datadesign/not-a-fan-of-the-report-manager-in-ssrs/
views:
  - 20823
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
I took some time off so apologize to readers for my lack of writing lately.

Today I thought I'd talk about the report manager and SSRS. Personally I'm not a big fan of it. It lands in there with SSMS and tasks like creating or modifying indexes. I use it when I need it really quick and it's the only thing I need to touch. If you have to modify or create a bunch of things it becomes very cumbersome and pretty much an annoyance. Any browser based front end is a dog. Sense report manager is just that, you can expect refresh issues, slow response and having to hit 30 “OK” buttons to get one thing saved. The nice thing about most SQL Server Services is they are controlled by system procedures, functions etc... This means in most cases you can utilize these same system objects to your advantage. One that I'll show you today is the create subscriptions procedure named, “CreateSubscription” for Reporting Services.

If you're are using SSRS and in a high reporting environment like most are, then you more than likely have dozens if not hundreds of subscriptions setup so reports are automatically delivered to users on a time basis. In most cases the business has critical tasks that reply on reports to be run at specific times. If those reports are not run at the exact time specified, there may be missed opportunities or missed issues that can be hidden by data later on. When I first started using SSRS years back I dreaded the task of creating a subscription for a report to run here and there through the day. It was time consuming and again, an annoyance. This lead me to run profiler and do a bit of reading on what SSRS does behind the scenes in report manager. Once I found the CreateSubscription procedure, creating a dozen scheduled subscriptions for days in the week was actually pretty easy. 

To run this procedure I recommend first looking at it to see what is does. You should always follow this guideline anytime you try to use objects like this. Follow the train all the way through to the end result set. An example of a really big catch on why you should do this can be seen in the CreateSubscription procedure. In order to gather the records required to insert the SID's for the person creating the subscription, the procedure GetUserIDBySid is called given the authority type of 1 being sent. If you do not accurately gather your SID before calling the CreatSubscription, you will essentially force the GetUserIDBySid to insert another row for your SUSER\_NAME value. That will essentially break down the integrity sense you will now have duplicates of your account listed in the Users table. So for this issue you should follow back to the Users table and see that the SID is stored as a varbinary(85) and you can gather that based on your SUSER\_NAME value as such

```sql
Set @my_usersid = (Select [SID] From ReportServer.dbo.Users Where UserName = suser_name())
```
Use this SID the modify and create user value.

The ExtensionSettings and Schedule are XML values so be careful on how you form them. THe best resource you will probably find on the XML values is by looking up the “CreateSubscription Method” on MSDN [here][1] 

The one thing that is another catch is the StartDateTime is based on a datetime value with the time zone offset included. If this is not formatted correctly, the report subscription is created but the schedule will fail. This creates a mess and will require you to remove the entire subscription to clear it up. To format this datetime I use a SQLCLR UDF as shown below

```CSHARP
public partial class UserDefinedFunctions
{
    [Microsoft.SqlServer.Server.SqlFunction]
    public static SqlString DateTimeTimeZoneOffset(DateTime datetime_sent)
    {
        return new SqlString(datetime_sent.ToString("yyyy-MM-ddTHH:mm:ss.fffzzzz"));
    }
};
```
You can then pass to this function a basic datetime value while only needing to worry about sending the time that you want over to it.

as such...

```sql
Set @time_send = (Select dbo.DateTimeTimeZoneOffset(Cast('2009-06-25 08:00:00' as datetime)))
```
So putting this all together you can come up with a script similar to the following. This tested will create a subscription for each day of the week to be sent out at 8:00 AM

```sql
Declare @me nvarchar(260)
Declare @now datetime
Declare @time_send varchar(35)
Declare @schedule nvarchar(1000)
Declare @report_id uniqueidentifier
Declare @report_name nvarchar(425)
Declare @my_usersid varbinary(85)

Set @report_name = '/{folder}/{reportname}'
Set @report_id = (Select ItemID From ReportServer.dbo.Catalog where [Path] = @report_name)
Set @now = getdate()
Set @me = SUSER_NAME()
Set @my_usersid = (Select [SID] From ReportServer.dbo.Users Where UserName = suser_name())
Set @time_send = (Select dbo.DateTimeTimeZoneOffset(Cast('2009-06-25 08:00:00' as datetime)))
Set @schedule = '<ScheduleDefinition xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
							<StartDateTime xmlns="http://schemas.microsoft.com/sqlserver/2006/03/15/reporting/reportingservices">' + @time_send  + '</StartDateTime>
							<WeeklyRecurrence xmlns="http://schemas.microsoft.com/sqlserver/2006/03/15/reporting/reportingservices">
								<WeeksInterval>1</WeeksInterval>
								<DaysOfWeek>
									<Monday>true</Monday>
									<Tuesday>true</Tuesday>
									<Wednesday>true</Wednesday>
									<Thursday>true</Thursday>
									<Friday>true</Friday>
								</DaysOfWeek>
							</WeeklyRecurrence>
							 </ScheduleDefinition>'

exec CreateSubscription @Report_Name=@report_name,
			@id=@report_id,
			@OwnerSid = @my_usersid,
			@OwnerName=@me,
			@OwnerAuthType=1,
			@Locale=N'en-US',
			@DeliveryExtension=N'Report Server Email',
			@InactiveFlags=0,
			@ExtensionSettings=N'<ParameterValues>
						<ParameterValue>
							<Name>TO</Name>
							<Value>enduser@emails.com</Value>
						</ParameterValue>
						<ParameterValue>
							<Name>BCC</Name>
							<Value>system_retention@dba.com</Value>
						</ParameterValue>
						<ParameterValue>
							<Name>ReplyTo</Name>
							<Value>NoReply@company.com</Value>
						</ParameterValue>
						<ParameterValue>
							<Name>IncludeReport</Name>
							<Value>True</Value>
						</ParameterValue>
						<ParameterValue>
							<Name>RenderFormat</Name>
							<Value>EXCEL</Value>
						</ParameterValue>
						<ParameterValue>
							<Name>Subject</Name>
							<Value>@ReportName was executed at @ExecutionTime</Value>
						</ParameterValue>
						<ParameterValue>
							<Name>IncludeLink</Name>
							<Value>True</Value>
						</ParameterValue>
						<ParameterValue>
							<Name>Priority</Name>
							<Value>NORMAL</Value>
						</ParameterValue>
					     </ParameterValues>',
			@ModifiedBySid = @my_usersid,
			@ModifiedByName=@me,
			@ModifiedByAuthType=1,
			@ModifiedDate=@now,
			@Description=N'Send e-mail to users and other descriptions',
			@LastStatus=N'New Subscription',
			@EventType=N'TimedSubscription',
			@MatchData=@schedule,
			@Parameters=N'<ParameterValues />',
			@Version=3
```
With all of this you can now create multiple subscriptions for the same report to run through the day while only changing the time entered. You can also modify this easily to utilize the method in a procedure. This makes it much cleaner and easier to dynamically send multiple times and multiple reports so you can create mass subscriptions with one call. I will try to get a well error handled and procedure like that up in the next few days for download.



\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][2] forum or our [Microsoft SQL Server Admin][3] forum**<ins></ins>

 [1]: http://msdn.microsoft.com/en-us/library/aa441019.aspx
 [2]: http://forum.ltd.local/viewforum.php?f=17
 [3]: http://forum.ltd.local/viewforum.php?f=22