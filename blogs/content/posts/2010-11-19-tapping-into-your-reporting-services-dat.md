---
title: Tapping Into Your Reporting Services Database
author: David Forck (thirster42)
type: post
date: 2010-11-19T15:18:17+00:00
ID: 953
excerpt: A look into 3 SQL Server views to help manage Reporting Services Instances
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/tapping-into-your-reporting-services-dat/
views:
  - 8499
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - reporting services
  - sql server 2005
  - sql server 2008
  - sql server 2008 r2
  - ssrs
  - t-sql

---
For every instance of SQL Server Reporting Services (2005+) there are two databases, a primary and a TempDB. The primary database holds all of the data for your instance, such as: Users, Folder Structure, Roles, Security, Subscriptions, Data Sources, etc. There a lot of data here that you can use to clean up your Reporting Services instances, or help keep them in order. Also, from my experience the database doesn’t change much from 2005 to 2008, so these should work on both versions. I’d be willing to bet they’ll work on 2008 R2 as well. If they don’t, let me know.

The very first view I made using data from the Reporting Services database was a list of Report Executions. This view was great because I could see which reports were actually being used and which weren’t, and who was using which reports. I made a report off of this view and provided it to a manager so they could track to make sure the other managers were checking their employee hours report.

sql
CREATE VIEW [dbo].[ExecutionLogView]
AS    SELECT TOP (100) PERCENT
            dbo.ExecutionLog.UserName,
            dbo.Catalog.Path,
            dbo.Catalog.Name,
            dbo.ExecutionLog.TimeStart,
            dbo.ExecutionLog.TimeDataRetrieval
      FROM
            dbo.ExecutionLog
            INNER JOIN dbo.Catalog
                  ON dbo.ExecutionLog.ReportID = dbo.Catalog.ItemID
      ORDER BY
            dbo.ExecutionLog.TimeStart DESC
```
One of the issues I had with one of my instances was subscriptions. There were scheduled subscriptions everywhere and a lot of them ran at the same time, but they weren’t on a shared schedule.

sql
create view dbo.ReportSubscriptionView
as
SELECT TOP (100) PERCENT
      dbo.Catalog.ItemID as ReportID,
      dbo.Catalog.Path,
      dbo.Catalog.Name,
      dbo.Catalog.Description,
      dbo.Catalog.Hidden,
      dbo.Subscriptions.SubscriptionID,
      dbo.Subscriptions.OwnerID,
      dbo.Subscriptions.Description AS SubscriptionDesc,
      dbo.Subscriptions.LastStatus,
      dbo.Subscriptions.EventType,
      dbo.Subscriptions.LastRunTime,
      dbo.Subscriptions.Parameters,
      dbo.Subscriptions.DataSettings,
      dbo.Subscriptions.DeliveryExtension,
      dbo.ReportSchedule.ScheduleID,
      dbo.Schedule.Name AS ScheduleName,
      dbo.Schedule.NextRunTime,
      dbo.Schedule.LastRunTime AS LastScheduleRun,
      dbo.Schedule.EndDate,
      dbo.Schedule.MinutesInterval,
      dbo.Schedule.DaysInterval,
      dbo.Schedule.WeeksInterval,
      dbo.Schedule.DaysOfWeek,
      dbo.Schedule.DaysOfMonth,
      dbo.Schedule.Month,
      dbo.Schedule.MonthlyWeek,
      dbo.Schedule.EventType AS ScheduleType,
      dbo.Schedule.ConsistancyCheck,
      dbo.Users.UserName as ModifiedBy
FROM
      dbo.Subscriptions
      INNER JOIN dbo.Catalog
            ON dbo.Subscriptions.Report_OID = dbo.Catalog.ItemID
      INNER JOIN dbo.Schedule
      INNER JOIN dbo.ReportSchedule
            ON dbo.Schedule.ScheduleID = dbo.ReportSchedule.ScheduleID
            ON dbo.Subscriptions.SubscriptionID = dbo.ReportSchedule.SubscriptionID
      INNER JOIN dbo.Users
            ON dbo.Subscriptions.ModifiedByID = dbo.Users.UserID
ORDER BY
      dbo.Catalog.Path,
      dbo.Catalog.Name
```
I discovered that SSRS creates a SQL Agent Job for every schedule subscription that isn’t on a shared subscription, which explained the Job explosion I had as well on the server. I went snooping and discovered that the Job that SSRS created is named the same as the ScheduleID in the above view. I used that view and that knowledge to create a few shared schedules and then using the view I found all of the relevant subscriptions in Report Manager and updated them to use the shared subscription. This reduced the number of jobs I had, and the number of different subscriptions I had. I was also able to identify subscriptions that were no longer used.

One other view that I found useful was a view of the whole directory and the related security.

sql
create view [dbo].[CatalogSecurity] as
select  top (100) percent
	c.ItemID,
	c.Path,
	c.Name,
	c.Type,
	c.PolicyID,
	u.UserName,
	r.RoleName
from dbo.Catalog c
	left outer join dbo.PolicyUserRole pcr
		on c.PolicyID=pcr.PolicyID
	left outer join dbo.Users u
		on pcr.UserID=u.UserID
	left outer join dbo.Roles r
		on pcr.RoleID=r.RoleID
order by Path, Type, UserName, RoleName
```

With this view, I could see who all had what rights to specific folders and reports. One of my initiatives has been to remove security from a by-report policy to a by-folder policy, making security a lot easier to manage.

Well, those are the main ones I’ve used. If you’ve got other useful views into the Reporting Services data, let me know, I’d love to see them.