---
title: Getting SSRS Subscriptions for a Specific Date
author: Kevin Conan
type: post
date: 2012-11-20T20:32:00+00:00
excerpt: |
  The Back Story
  
  
  This past weekend, work was being down on our exchange server, so e-mail was down most of Saturday.  What everyone (myself included) failed to realise was that this was going to impact scheduled reports from SSRS.
  
  So Monday mornin&hellip;
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/getting-ssrs-subscriptions-for-a/
views:
  - 15455
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server Admin
  - SSRS

---
## The Back Story

This past weekend, work was being done on our exchange server, so e-mail was down most of Saturday. What everyone (myself included) failed to realize was that this was going to impact scheduled reports from SSRS.

So Monday morning, I got an e-mail asking about which reports should have ran on the Saturday and who they would have gone to.

It sounds like such a simple request that I figured it couldn&#8217;t be that hard to figure it out. I was dead wrong. 

## The Hurdles

<div class="image_block">
  <a href="/wp-content/uploads/users/kconan/hurdles.jpg?mtime=1353450411"><img alt="" src="/wp-content/uploads/users/kconan/hurdles.jpg?mtime=1353450411" width="400" height="338" /></a>
</div>

After poking around in SSRS Web Interface, I quickly found that short of opening each and every subscription there was no way to find this information. Next I took a peek in the ReportServer database where SSRS is hosted and it looked pretty simple. I peeked in a few tables and the column names all lined up nice for joining to the other tables. But then I saw the first of two hurdles that wouldn&#8217;t be so easy to get past.

## Subscriptions.ExtensionSettings

I found the e-mail addresses that each Subscription is e-mailed to. The problem (or opportunity) is that it is buried in an XML field &#8211; Subscriptions.ExtensionSettings. This is the first opportunity that I&#8217;ve really had to mess with XML in SQL in a long time so I figured it wouldn&#8217;t be too bad and I could figure out how to pluck what I needed.

However, my heart sank when I hit the second hurdle. 

## Schedule.RecurrenceType, Schedule.DaysOfWeek and Schedule.DaysOfMonth

<div class="image_block">
  <a href="/wp-content/uploads/users/kconan/SSRS_Schedule.JPG?mtime=1353450271"><img alt="" src="/wp-content/uploads/users/kconan/SSRS_Schedule.JPG?mtime=1353450271" width="505" height="136" /></a>
</div>

The Schedule table has a series of fields where it stores the schedule &#8211; Schedule.RecurrenceType, Schedule.DaysOfWeek and Schedule.DaysOfMonth. This one wasn&#8217;t going to be as straight forward to get past. I remember something in the back of my mind about taking a number have to square it or something or another but it was all really fuzzy.

## This can&#8217;t be the first time someone has had to solve this issue, who&#8217;s got a blog or forum post about it?!?!

At this point, I figured it was time to turn to the web. This has to be an issue that others have come across, so how did they solve it? After searching around for a while, I wasn&#8217;t able to find anyone who broke down the schedule fields. In fact, the only responses from Microsoft on their forums was that they don&#8217;t support querying them! It was time for some bigger guns so I turned to twitter and posted on #SQLHELP and #SSRSHELP. 

After a little while and a couple of conversations, people gave me the idea of linking the Schedules to their jobs and pulling the schedules that way. Linking the Schedules to their Jobs was easy. However, the job schedules are also difficult to figure out because they are done in a similar way.

## Ah, there&#8217;s light at the end of this tunnel!

<div class="image_block">
  <a href="/wp-content/uploads/users/kconan/tunnel.jpg?mtime=1353450579"><img alt="" src="/wp-content/uploads/users/kconan/tunnel.jpg?mtime=1353450579" width="300" height="224" /></a>
</div>

The difference between job schedules and subscription schedules is that there ARE posts out there on how to decipher them! Between the little I was able to find about the schedules and the information about the job schedules, now I had what I needed to decipher the Subscription Schedules.

Remember, I needed to know what report subscriptions should have ran on a specific day.

Schedule.RecurrenceType had 3 values that I needed:

2 &#8211; The subscription runs multiple times during the day, every day. So I want all subscriptions that have that setting.
  
4 &#8211; The subscription runs one or more days a week. This is used in conjunction with Schedule.DaysOfWeek.
  
5 &#8211; The subscription runs on specific days of the month. This is used in conjunction with Schedule.DaysOfMonth.

I&#8217;m not going to explain bit wise, but here is a link to a BOL Article:

<http://msdn.microsoft.com/en-us/library/ms174965.aspx>

Schedule.DaysOfWeek and Schedule.DaysOfMonth work off a bit checker (bit wise). It&#8217;ll help if you think of the day of the week in terms of what number it is. For example, Sunday = 1, Monday = 2, Tuesday = 3 etc.

The first value (Sunday and the first of the month) use a bit value of 1. After that you can figure out the bit value by taking 2 the power of the number you are looking for minus 1. For example, for the 3rd, it would be 2 to the power of (3 &#8211; 1) which is 2 to the power of 2 which equals 4.

So if we were looking for reports that ran on every Tuesday, the TSQL would be Schedule.RecurrenceType = 4 AND Schedule.DaysofWeek & 4 = 4.

## The Query

I cleaned up the query a little bit by having a variable called @Date that you would set to the date you want to know which subscriptions would have been run on.

The query returns the Path to the Report, the Report Name, the owner of the Subscription and which E-Mail addresses it would have been sent to.

This by no means a finished and completely polished query, but rather a good starting point for others to use for their purposes.

<pre>DECLARE	 @Date			DATETIME
		,@DaysOfWeek	INT
		,@DaysOfMonth	INT;
		
SET @Date	= '2012-11-17 00:00:00.000';

SELECT @DaysOfWeek	= CASE DATEPART(DW,@Date)
						WHEN 1 THEN 1  --Sunday
						WHEN 2 THEN 2  --Monday
						WHEN 3 THEN 4  --Tuesday
						WHEN 4 THEN 8  --Wednesday
						WHEN 5 THEN 16 --Thursday
						WHEN 6 THEN 32 --Friday
						WHEN 7 THEN 64 --Saturday
					  END;		
						
SELECT @DaysOfMonth	= CASE DATEPART(D,@Date)
						WHEN 1 THEN 1
						ELSE POWER(2,(CAST(DATEPART(D,@Date) AS INT) - 1))
					  END;
					  
SELECT DISTINCT
		 SUB.ReportPath
		,SUB.ReportName
		,SUB.ReportOwner
		,EMail.value('Value[1]','VARCHAR(1000)') as EMailAddresses		
  FROM	(
	SELECT 	 C.[Path] AS ReportPath
			,C.Name AS ReportName
			,U.UserName AS ReportOwner
			,CONVERT(XML,SB.ExtensionSettings) AS Ext
	  FROM ReportServer.dbo.ReportSchedule RS
	  JOIN ReportServer.dbo.Schedule S 
	    ON S.ScheduleID = RS.ScheduleID
	   AND S.RecurrenceType = 2
	    OR (S.RecurrenceType = 4 AND (S.DaysOfWeek &amp; @DaysOfWeek = @DaysOfWeek))
	    OR (S.RecurrenceType = 5 AND (S.DaysOfMonth &amp; @DaysOfMonth = @DaysOfMonth))
	  JOIN ReportServer.dbo.[Catalog] C 
	    ON C.ItemID = RS.ReportID
	  JOIN ReportServer.dbo.Subscriptions SB 
	    ON SB.SubscriptionID = RS.SubscriptionID
	  JOIN ReportServer.dbo.Users U 
	    ON U.UserID = SB.OwnerID
		) SUB
 CROSS APPLY Ext.nodes('/ParameterValues/ParameterValue') AS SubEMail(EMail) WHERE 	EMail.value('Value[1]','VARCHAR(1000)') LIKE '%[^ ]@%';</pre>