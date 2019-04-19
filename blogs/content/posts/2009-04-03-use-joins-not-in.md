---
title: Use Joins, Not IN
author: Alex Ullrich
type: post
date: 2009-04-03T21:52:06+00:00
ID: 373
url: /index.php/datamgmt/dbprogramming/use-joins-not-in/
views:
  - 12229
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server

---
While untangling a pretty nasty correlated subquery someone had written at work that was going three layers deep with IN (select X from Y) type of stuff, I used twitter to vent some of my rage [SQL Training Wheels][1]. This found its way to facebook, where one of my friends (and fellow LessThanDot members) asked “How So”.

Well, the answer is simple. Like training wheels, they are easy. Damn easy. But, also like training wheels, they can really get in the way. As such, use of IN is often good for quick ad-hoc queries, but for anything to be run more than once it's probably worth at least exploring the option of using a derived table and a join.

Ok here is a second try at an example. The query in question was meant to get the most recent appointment for each invoice, when there is not really a direct link. So take some sample data:

```sql
--invoices, invoice activities, appointments

create table #invoices (id int identity(1,1) primary key clustered, bill_date datetime)
create table #invoice_activities (id int identity(1,1) primary key clustered, invoice_id int, activity_date datetime, cost smallmoney)
create table #activities_appointments (act_id int, appt_id int)
create table #appointments (id int identity(1,1) primary key clustered, appointment_date datetime) 


insert #invoices 
select GETDATE()
union all select GETDATE()-1
union all select GETDATE() - 4

insert #invoice_activities 
select 1, GETDATE() - 5, 426.6
union all select 1, GETDATE() - 7, 435.9
union all select 2, GETDATE()-10, 100.4
union all select 3, GETDATE() - 15, 1023.54
union all select 3, GETDATE() - 20, 195.32
union all select 3, GETDATE() - 25, 191.65

insert #activities_appointments
select 1, 1
union all select 2,2
union all select 4, 3
union all select 5, 4
union all select 6, 5 

insert #appointments
select GETDATE() - 5
union all select GETDATE() - 7
union all select GETDATE() - 10
union all select GETDATE() - 20
union all select GETDATE() - 25
```

and two different ways to approach it:

```sql
select id,
	(select max(appointment_date)
		from #appointments
		where id in (
			select distinct(appt_id)
			from #activities_appointments
			where act_id in (
				select id 
				from #invoice_activities
				where invoice_id = i.id
			)
		)
	)
from #invoices i

select i.id, MAX(appointment_date)
from #invoices i
left join #invoice_activities ia
on i.id = ia.invoice_id
left join #activities_appointments aa
on ia.id = aa.act_id
left join #appointments a
on aa.appt_id = a.id
group by i.id
```

Notice that one uses a correlated subquery and some IN's, and the other uses simple joins. For this example, there is not much performance difference (most queries are pretty easy with a small data set) but in the real database there are several million rows in each table, and one of the tables involved takes up about 1/2 of the entire database for the application we support. 

The example from work was a bit more complex (it wasn't straightforward joins, and I needed to create a pretty gross derived table to get the dataset I wanted to join to) but the performance gain was much greater. Because the correlated subquery as I understand it gets called 1x per row in the resultset, and it was not exactly a lightweight query, the original query my coworker had written took 15-30 seconds to return a relatively small resultset. The more rows you want back, the worse it gets. 

By joining to a derived table we were able to get the query down below 2 seconds. This gain was probably more an indication of how bad the subquery really was (if you think my example is ridiculous, try thinking of one ten times more so!) but I think the lesson holds that you don't want to always use IN just because its' more comfortable.

For another reason I dislike IN, check out [Denis' Old Post][2]

\*** **Got a SQL Server question? Check out our [Microsoft SQL Server Programming][3] forum or our [Microsoft SQL Server Admin][4] forum**<ins></ins>

 [1]: http://twitter.com/AlexCuse/status/1447655426
 [2]: http://sqlservercode.blogspot.com/2007/04/you-should-never-use-in-in-sql-to-join.html
 [3]: http://forum.ltd.local/viewforum.php?f=17
 [4]: http://forum.ltd.local/viewforum.php?f=22