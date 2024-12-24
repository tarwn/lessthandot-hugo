---
title: Grouping and merging data with CROSS APPLY
author: Axel Achten (axel8s)
type: post
date: 2012-03-12T12:14:00+00:00
ID: 1555
excerpt: 'The first four letters in database administrator spell data so for some reason people see you as somebody who can do magic with all kinds of data. One of the requests I got was to start from an export of course data and to group the courses and merge th&hellip;'
url: /index.php/datamgmt/dbprogramming/grouping-and-merging-data-with/
views:
  - 6153
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
tags:
  - cross apply
  - for xml
  - merging columns
  - sql server

---
The first four letters in database administrator spell data so for some reason people see you as somebody who can do magic with all kinds of data. One of the requests I got was to start from an export of course data and to group the courses and merge the dates in a single column. I had to start with an Excel document and the result had to be in CSV format. For the import and export I used the Import/Export Wizard in real life but for this blog post I'll use some sample data.
  
First things first, let's create a table to hold the data:

```sql
CREATE TABLE CourseData (
	CourseCode varchar (10),
	CourseName varchar (100),
	CourseStartDate date
	)
GO
```
Now let's insert some data. Note that there are several courses with the different start dates. This was how the excel was given to me.

```sql
DECLARE @i int = 1
WHILE @i < 4
BEGIN
INSERT INTO CourseData
	VALUES ('10775A','Administering Microsoft SQL Server 2012 Databases',DATEADD(m,@i,getdate())),
			('10776A','Developing Microsoft SQL Server 2012 Databases',DATEADD(m,@i,getdate())),
			('10777A','Implementing a Data Warehouse with Microsoft SQL Server 2012 Databases',DATEADD(m,@i,getdate())),
			('10774A','Querying Microsoft SQL Server 2012',DATEADD(m,@i,getdate())),
			('10778A','Implementing Data Models and Reports with Microsoft SQL Server 2012',DATEADD(m,@i,getdate()))
SET @i = @i + 1
END
GO
```
The result looks like this:

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/Axel8s/CrossApply1.png?mtime=1331554009"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/Axel8s/CrossApply1.png?mtime=1331554009" width="488" height="284" /></a>
</div>

Now if you look at the data you'll see we have 3 lines per course each with a different date. The request was to have 1 line per course with all the dates in one field. To do this I'm "joining" the table to itself by using it as a normal table once and as XML the second time. Using the DISTINCT clause on the left side filters out the duplicate records. The FOR XML on the right side is used to make a dummy list of the dates the courses start. With the CROSS APPLY operator I'm joining the two results in a result set like requested by the customer:

```sql
SELECT DISTINCT CourseCode, CourseName, Datelist 
	FROM CourseData AS o
CROSS APPLY
(SELECT CONVERT(varchar(10),CourseStartDate,103) + ' '
	FROM CourseData AS x
	WHERE o.CourseCode = x.CourseCode
	FOR XML path('')) AS dummy(datelist)
GO
```
With the desired result:

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/Axel8s/CrossApply2.png?mtime=1331554024"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/Axel8s/CrossApply2.png?mtime=1331554024" width="594" height="156" /></a>
</div>

So maybe the end-user was right and we can do magic with data. I didn't use the tools he expected but importing the Excel file into a SQL Server database brought it to my comfort zone where I could use the XML and CROSS APPLY possibilities of SQL Server to get the desired result.