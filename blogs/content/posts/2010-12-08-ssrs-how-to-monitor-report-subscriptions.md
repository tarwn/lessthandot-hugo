---
title: 'SSRS: How to monitor report subscriptions'
author: ptheriault
type: post
date: 2010-12-08T12:25:06+00:00
excerpt: 'As we all know SQL Server Reporting Services is a very powerful tool that gives end users a multitude of ways to retrieve data.  One such way is through subscriptions.   Subscriptions can be created by both Administrators and end users.  So as an admini&hellip;'
url: /index.php/datamgmt/datadesign/ssrs-how-to-monitor-report-subscriptions/
views:
  - 21274
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
As we all know SQL Server Reporting Services is a very powerful tool that gives end users a multitude of ways to retrieve data. One such way is through subscriptions. Subscriptions can be created by both Administrators and end users. So as an administrator it is difficult to keep track of what subscriptions are running and when they are running. It can also come as a surprise when the Director of a department comes to you to ask where his report was this morning. As a DBA we must always know before the end user when there is a problem. So how can we know if subscriptions are running successfully? There is actually a very simple query. When a subscription is created a record is added to the dbo.Subscriptions table in the ReportServer database. There is also column in that table named LastStatus. That column is update with the LastStatus of each subscription after the subscription is executed. I have written a query that selects records where the LastStatus was not successful. The path field in the query will show you where you find the report. This way you can go in to correct the subscription if the problem is with the email recipient.

<pre>select s.LastRunTime,
       s.LastStatus, 
       s.Description,
       c.Path,
       c.name,
       u.UserName as SubscriptionOwner
from subscriptions s
JOIN users u on s.OwnerId = u.UserId
JOIN Catalog c on s.Report_OID = c.ItemID
WHERE LastStatus like '%Failure%'
Or LastStatus like '%Error%'
or LastStatus like '%The e-mail address of one or more recipients is not valid.%'
or LastStatus like '%Thread was being aborted.%'
Order by LastRunTime</pre>

I have not been able to find a list of distinct or possible LastStatus values. That is why I use key words like Failure or error. If any knows of that list feel free to add a comment on where to find it.
  
I have also taken this query and made a report that can be run from SSRS. I have granted permissions on the report to our Helpdesk. This allows them to run it as part of their morning processes. They can then be proactive in trouble shooting failed report subscriptions.
  
One other note about subscriptions, most end users who create their own subscriptions schedule them to run just before they come in for the day. So you will find that most subscriptions are running first thing in the morning. You may choose to monitor this and ask end users to run spread them out over larger time period to balance the load.