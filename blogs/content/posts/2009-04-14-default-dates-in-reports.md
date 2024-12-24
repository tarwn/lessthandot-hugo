---
title: Default Dates in SSRS Reports
author: pmch22
type: post
date: 2009-04-14T12:05:17+00:00
ID: 385
url: /index.php/datamgmt/datadesign/default-dates-in-reports/
views:
  - 6115
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
For reports that requires start and end dates as parameters, typically the default dates are set as today and today + 1 day or today + 1 month. Instead it might be a good idea to pick up the start and end dates from the database. This way the user will not have to play the guessing game of trying different date ranges to generate a valid report.
  
To do this, create a new dataset. Paste the below query 

```sql
Declare @joindate datetime
select @joindate = max(joindate)  from employees
select cast(convert (varchar(10),dateadd(dd,-(day(@joindate)-1),@joindate),101) as datetime) as startdate,  cast(convert(varchar(10),dateadd(dd,-day(dateadd(mm,1,@joindate)),dateadd(mm,1,@joindate)),101) as datetime) as enddate
```
Next on the Reports Parameters window, set the default values as – From Query and select the Start and End dates.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/date-parameters.GIF" alt="" title="" width="666" height="520" />
</div>

Next time when you run the report, the start and end dates will be picked up from the database. You can use these dates to generate the report by default.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/Date-Range.gif" alt="" title="" width="749" height="56" />
</div>