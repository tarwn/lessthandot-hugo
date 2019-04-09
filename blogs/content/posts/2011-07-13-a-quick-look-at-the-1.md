---
title: A Quick look at the new EOMONTH function in SQL Server Denali CTP3
author: SQLDenis
type: post
date: 2011-07-13T14:09:00+00:00
ID: 1250
excerpt: |
  SQL Server Denali CTP3  has a bunch of new date/time functions like DATEFROMPARTS,  DATETIMEFROMPARTS and EOMONTH
  
  First let's take a look at EOMONTH.
  
  The syntax for EOMONTH is
  
  EOMONTH ( start_date [, month_to_add ] )
  
  If you pass in getdate()&hellip;
url: /index.php/datamgmt/datadesign/a-quick-look-at-the-1/
views:
  - 9130
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - dates
  - denali
  - functions

---
SQL Server Denali CTP3 has a bunch of new date/time functions like DATEFROMPARTS, DATETIMEFROMPARTS and EOMONTH

First let's take a look at EOMONTH.

The syntax for EOMONTH is

<pre>EOMONTH ( start_date [, month_to_add ] )</pre>

If you pass in getdate() you will get the last day of the month for the current month

sql
SELECT EOMONTH(getdate())
```

2011-07-31 00:00:00.000

If you pass in a date, you will also get the last date for that month

sql
SELECT EOMONTH('20110615')
```

2011-06-30 00:00:00.0000000

This function also accepts an optional parameter: month\_to\_add

_month\_to\_add
  
Optional integer expression specifying the number of months to add to start_date.</p> 

If this argument is specified, then EOMONTH adds the specified number of months to start_date, and then returns the last day of the month for the resulting date. If this addition overflows the valid range of dates, then an error is raised.</em>

So if we pass 1 for month\_to\_add it will add a month

sql
SELECT EOMONTH('20110615',1)
```

2011-07-31 00:00:00.0000000

If we pass -1 for month\_to\_add it will subtract a month

sql
SELECT EOMONTH('20110615',-1)
```

2011-05-31 00:00:00.0000000

The one problem with this function is that if you do a query and specify between some date and EOMONTH it won't give you anything after midnight. I already explained that in this post: [How Does Between Work With Dates In SQL Server?][1]

I also wonder why there is no SOMONTH function? Yes, I know it starts with 1, but if there is an _end of month_ function then someone will also search for a _start of month_ function.

 [1]: /index.php/DataMgmt/DataDesign/how-does-between-work-with-dates-in-sql-