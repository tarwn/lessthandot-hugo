---
title: 'Client Statistics in SSMS.  Check execution times'
author: Ted Krueger (onpnt)
type: post
date: 2009-03-22T22:53:17+00:00
ID: 362
url: /index.php/datamgmt/datadesign/client-statistics-in-ssms/
views:
  - 9911
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Microsoft SQL Server

---
How many times have you done something like this testing speed

```sql
Declare @cnt_test bigint
Declare @st datetime
Set @st = getdate()
Set @cnt_test = (Select count(*) From dbo.test_scan)
Select DateDiff(ms,@st,getdate())
```
Specifically the DateDiff() in milliseconds to see how long the execution took.

There is a nice little option in SSMS that I leave on when working on a query over time. It's called Client Statistics. When set on you can get the same results and much more that can help you measure your performance while altering your code. The results will show the same as having the show execution plan on in SSMS as a tab in the results window. The results from client statistics will accumulate over the time you alter your query also. This is a very nice option and valuable when measuring your changes without adding code you will only have to remove. It will even give you an arrow showing in red if your execution time has gone up or green pointing down for improving

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//client_stats.gif" alt="" title="" width="772" height="367" />
</div>

The option in SSMS 2005 can be found here

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//ssms2005stats.gif" alt="" title="" width="634" height="72" />
</div>

In SSMS 2008 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//ssms2008stats.gif" alt="" title="" width="726" height="137" />
</div>

Although the DateDiff and measuring milliseconds and nanoseconds (in 2008) is still required to pinpoint spots in your code, the client statistics measurement is really a nice added review of you query performance.