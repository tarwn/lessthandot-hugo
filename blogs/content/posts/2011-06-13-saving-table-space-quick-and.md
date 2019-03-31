---
title: Saving Table Space (Quick And Dirty)
author: David Forck (thirster42)
type: post
date: 2011-06-13T21:07:00+00:00
excerpt: 'One of the quickest and easiest ways to make a database more performant is to reduce how much space the data takes up.  Here’s a script that I wrote that’ll find each table in a database (run it in the context of the database).  This script determines h&hellip;'
url: /index.php/datamgmt/datadesign/saving-table-space-quick-and/
views:
  - 4820
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Microsoft SQL Server Admin

---
One of the quickest and easiest ways to make a database more performant is to reduce how much space the data takes up. Here’s a script that I wrote that’ll find each table in a database (run it in the context of the database). This script determines how many rows of data each table has (in kilobytes), determines the size of the data in the table, and then gives you a ratio of data per row. The higher a data/row ratio the more likely there is a chance of reducing the amount of space (note that I’m not looking at table indexes or fill factors, those are another topic to cover).

<pre>declare @tables table (name varchar(max), ID int identity(1,1), cnt int, size int)
declare @i int, @count int, @name varchar(max), @sql varchar(max)

insert into @tables
        (name)
select 
TABLE_SCHEMA + '.' + TABLE_NAME
from INFORMATION_SCHEMA.tables
where TABLE_TYPE='base table'

 

select @count=count(*) from @tables
set @i=1

while @i&lt;=@count
begin

	create table #temp
	(
		name varchar(max),
		rows varchar(max),
		reserved varchar(max),
		data varchar(max),
		index_size varchar(max),
		unused varchar(max)
	)

	select @name=name from @tables where ID=@i
	insert into #temp
	(
		name, rows, reserved, data, index_size, unused
	)
	exec sp_spaceused @name
	
	update @tables
		set size=left(data,len(data)-3),
		cnt=rows
	from #temp a
		cross join @tables b
	where b.id=@i


	drop table #temp
	set @i=@i+1
end


select *,
(size*1.0)/cnt as Ratio
from @tables
where cnt&gt;0
order by (size*1.0)/cnt desc</pre>

So after this runs on a database the first row will be the table that has the highest ratio. Here are some quick ways to reduce your data size:
  
• Change nvarchar to varchar
  
• Reduce decimal precision
  
• Change unnecessary chars to varchars (and update the data to get rid of the extra spacing)
  
• Change ints to bits (when applicable)
  
• Change datetimes to dates
  
• Remove unnecessary columns
  
• Remove dead rows
  
• Reduce big int > int > small int > tinyint

Now I know some people might be thinking that some of these seem like very small changes, but one byte multiplied by 1k is still 1 kb of data. If you can effectively remove 10 bytes per row (which isn’t that hard depending on the data types), in a 1k table you can effectively save 10kb of data. That’s 10kb less of data that has to be accessed in memory, accessed from the hard drive, sent over the wire, backed up, or possibly stored in index/es, all on just one table!

Remember to always research your changes and test everything first before pushing changes to a production system; you never know when something might need that extra piece of data.

One way that you might be interested in modifying the script is by changing the where clause to have a ratio of >=8. This is signifigant because sql server stores data on 8k pages, and if you&#8217;re completely filling, or going over that, you could have additional speed and space issues. You could also set the count minimum to something like 10 or so to get rid of any small lookup tables you may have.

For more information on data types and sizes, look [here][1]

 [1]: http://msdn.microsoft.com/en-us/library/ms187752.aspx