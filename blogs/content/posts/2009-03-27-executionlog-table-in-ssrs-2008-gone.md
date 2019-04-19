---
title: ExecutionLog Table in SSRS 2008 Gone
author: Ted Krueger (onpnt)
type: post
date: 2009-03-27T12:05:43+00:00
ID: 369
url: /index.php/datamgmt/datadesign/executionlog-table-in-ssrs-2008-gone/
views:
  - 7443
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Microsoft SQL Server Admin

---
You may notice that now in SSRS 2008 you are missing a critical reporting table named ExecutionLog. Well, you really aren't missing it.

This table was the bread and butter of trending for administrators. If you weren't using it to gauge your reporting services activity then you should. A simple query like

```sql
select Count(*) cnt,[Name] ReportName
from executionlog a
join catalog b on a.reportid = b.itemid
Where 
	convert(varchar(10),timeend,101) >= @st 
	And convert(varchar(10),timeend,101) <= @end
	And [Status] = 'rsSuccess'
group by reportid,[Name]
order by [name]
```
Could buy you a new reporting server. Don't forget we need baseline proof to justify upgrades. This could even push version upgrades. 

So all of your reports and DBA troubleshooting scripts are going to die a quick and painful death. Nope! Actually you may have not noticed this change because there is now a view ExecutionLog. Personally I don't care for this change. It adds alterations to my administration of SSRS at the DB level. We can't change it now though! 

The new table name is ExecutionLogStorage. I have a fairly large ReportServer DB and have tested the performance of my reporting off this data for trending and came to the conclusion there really isn't much of a change other than the objects are different. 

While you are checking this out, take a look at another view added to SSRS 2008 named ExecutionLog2. It adds some detailed information and actually may be more beneficial to your needs. 

So just be warned that table had a sex change to a view 😉