---
title: Replace Your Case When Then Else Syntax With the Sign Function In SQL Server
author: SQLDenis
type: post
date: 2009-06-22T12:50:30+00:00
ID: 479
url: /index.php/datamgmt/datadesign/replace-your-case-when-then-else-syntax/
views:
  - 10326
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
tags:
  - functions
  - howto
  - sql server 2000
  - sql server 2008

---
If you want to show a 1 when there is a value for something in the column and 0 if none of the rows have that values you typically do something like this

```sql
CASE WHEN SUM(CONVERT(INT,SomeValue)) > 0 THEN 1 ELSE 0 END
```

Basically you sum it up and if the sum is greater than 0 then you show 1 otherwise you show 0
  
Here is what it might look like in code

First create the following table with this data

```sql
create table #Cars(id int,brand varchar(20),HasDefects bit)

insert #Cars values(1,'Chevy Corvette',1)
insert #Cars values(2,'Ford Taurus',0)
insert #Cars values(3,'Ford Taurus',1)
insert #Cars values(4,'BMW 635 CSi',0)
insert #Cars values(5,'BMW 635 CSi',0)
insert #Cars values(6,'Fiat 500',1)
insert #Cars values(7,'Fiat 500',1)
```

And here is our CASE WHEN THEN ELSE query

```sql
SELECT brand,CASE WHEN SUM(convert(int,HasDefects)) > 0 THEN 1 ELSE 0 END AS HasDefects
from #Cars
group by brand
```

We need to convert HasDefects to an integer before using the sum function, otherwise you will get the following error

_Server: Msg 409, Level 16, State 2, Line 1
  
The sum or average aggregate operation cannot take a bit data type as an argument._

So how can we change that to use the sign function? It is very easy all you have to do is wrap the sign function around the sign function

```sql
select brand, sign(sum(convert(int,HasDefects))) as HasDefects
from #Cars
group by brand
```

Here is our output
  
——————–

<pre>brand	HasDefects
BMW 635 CSi	0
Chevy Corvette	1
Fiat 500	1
Ford Taurus	1</pre>

As you can see that made the code smaller by about 20 bytes. This of course will only work if you want 0 and 1; if you want more posibilities then you need to use case. Another reason why you maybe don't want to use the sign function is that someone looking at your code might not immediately know what the code is supposed to do.
  
Remember you should always write code with the assumption that the person who will be maintaining your code is a psychopatic killer who has your address 😉

How does the sign function work? 

If the value is negative then -1 is returned
  
If the value is positive then 1 is returned
  
If the value is 0 then 0 is returned
  
If the value is NULL then NULL is returned

Scale will affect the output also; if you use -22.0001 then -1.0000 will be returned and if you use -22.01 then -1.00 will be returned
  
Here is a query you can run to see what sign returns for different values

```sql
select 	sign (0),  -- 0
	sign (1),  -- 1
	sign (-1), -- -1
	sign (null), --null
	sign (-200), -- -1
	sign (200),  -- 1
	sign (-22.0001), -- -1.0000
	sign (22.0001),  -- 1.0000
	sign (-22.01), -- -1.00
	sign (22.01)  -- 1.00
```

On our wiki there is an article that shows you another 9 lesser know functions; these functions are

BINARY_CHECKSUM
  
COLUMNPROPERTY
  
DATALENGTH
  
ASCII, CHAR,UNICODE
  
NULLIF
  
PARSENAME
  
STUFF
  
REVERSE
  
GETUTCDATE

You can find that article here [Ten SQL Server Functions That You Have Ignored Until Now][1]



\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][2] forum or our [Microsoft SQL Server Admin][3] forum**<ins></ins>

 [1]: http://wiki.ltd.local/index.php/Ten_SQL_Server_Functions_That_You_Have_Ignored_Until_Now
 [2]: http://forum.ltd.local/viewforum.php?f=17
 [3]: http://forum.ltd.local/viewforum.php?f=22