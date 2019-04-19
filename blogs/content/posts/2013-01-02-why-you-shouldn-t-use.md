---
title: "Why you shouldn't use SELECT *"
author: Axel Achten (axel8s)
type: post
date: 2013-01-02T11:28:00+00:00
ID: 1894
excerpt: "A customer's DBA team created a checklist for the development teams with some best practices for writing proper T-SQL and asked me to write some contributions for their tips document library. So if I do the research and write the documents I might as we&hellip;"
url: /index.php/datamgmt/dbprogramming/why-you-shouldn-t-use/
views:
  - 13436
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - select
  - select star
  - t-sql

---
A customer's DBA team created a checklist for the development teams with some best practices for writing proper T-SQL and asked me to write some contributions for their tips document library. So if I do the research and write the documents I might as well post them here. The content may not be all sparkling and new but since there is a demand from customers, there are still people out there having trouble finding the correct information.
  
So let's get started. The first document is why you should avoid SELECT * in your queries:

**For performance reasons**

To be honest this part of the post is based on Ted's post: [SELECT * vs SELECT 1 with EXISTS][1].
  
To check the performance impact I use a tool called [SQLQueryStress][2] to execute my queries and see how much time elapsed.
  
To get started I need a table with a large number of columns, data is not necessary for this test so I use this script to generate a table with 127 columns:

```sql
DECLARE @colnumber int = 1
DECLARE @command VARCHAR(4000) =''

WHILE @colnumber <= 125
	BEGIN
		SET @command = @command + ' col' + CAST(@colnumber AS varchar(3)) + ' int, '
		SET @colnumber += 1
	END
SET @command = 'CREATE TABLE StarPerform (PerfId int, ' + @command + ' Lastcolumn int)'

EXEC (@command)
```
Now I query the table using the SQLQueryStress tool and I choose a high Number of Iterations to get a meaningful average:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/StarPerform1.JPG?mtime=1357132527"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/StarPerform1.JPG?mtime=1357132527" width="1361" height="430" /></a>
</div>

In the Client Seconds/Iteration (Avg) you see that the performance difference for an individual query is negligible but a similar query often executed on a busy server can result in 15 extra seconds on 5000 Iterations.

**Broken code**

Using SELECT * in Views is also a bad practice because changes to the underlying table will return unexpected results or fail completely.
  
First I create a table and insert some data:

```sql
--Create the table
CREATE TABLE StarBreak
 (
	ID int IDENTITY (1,1),
	Name varchar (10),
	DateFirstPost date,
	DateLastPost date
);
GO

--Insert some values
INSERT INTO StarBreak (Name, DateFirstPost, DateLastPost)
	VALUES ('Denis','20080207','20130101'),('Ted','20081107','20121231'),('Koen','20121123','20121227'),('Jes','20101210','20121221');
GO
```
Now I create and query a View to return all the columns:

```sql
CREATE VIEW GetStarFromStarBreak
	AS
		SELECT * from Starbreak;
		
GO
```
And I get this result back:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/StarPerform2.JPG?mtime=1357132538"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/StarPerform2.JPG?mtime=1357132538" width="261" height="95" /></a>
</div>

Now let's drop the table and recreate it but switch the position of the two datecolumns:

```sql
--Drop the table
DROP TABLE StarBreak;
GO

--Create the table
CREATE TABLE StarBreak
 (
	ID int IDENTITY (1,1),
	Name varchar (10),
	DateLastPost date,
	DateFirstPost date
);
GO

--Insert some values
INSERT INTO StarBreak (Name, DateFirstPost, DateLastPost)
	VALUES ('Denis','20080207','20130101'),('Ted','20081107','20121231'),('Koen','20121123','20121227'),('Jes','20101210','20121221');
GO
```
When I query the table again I get the correct result:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/StarPerform3.JPG?mtime=1357132547"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/StarPerform3.JPG?mtime=1357132547" width="262" height="97" /></a>
</div>

But when I query my view again I get the following result:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/StarPerform4.JPG?mtime=1357132561"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/StarPerform4.JPG?mtime=1357132561" width="261" height="95" /></a>
</div>

You see that the column headers are in the same order as in the initial table but the data reflects the column order of the second table.
  
Dropping the table, and adding a column in the middle will also result in the above behavior:

```sql
--Drop the table
DROP TABLE StarBreak;
GO

--Create the table
CREATE TABLE StarBreak
 (
	ID int IDENTITY (1,1),
	Name varchar (10),
	Gender char(1),
	DateLastPost date,
	DateFirstPost date
);
GO

--Insert some values
INSERT INTO StarBreak (Name, DateFirstPost, DateLastPost)
	VALUES ('Denis','20080207','20130101'),('Ted','20081107','20121231'),('Koen','20121123','20121227'),('Jes','20101210','20121221');
GO
```<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/StarPerform5.JPG?mtime=1357132571"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/StarPerform5.JPG?mtime=1357132571" width="258" height="96" /></a>
</div>

What happens when I recreate the table but only 3 instead of 4 columns? Let's try:

```sql
--Drop the table
DROP TABLE StarBreak;
GO

--Create the table
CREATE TABLE StarBreak
 (
	ID int IDENTITY (1,1),
	Name varchar (10),
	DateLastPost date,
);
GO

--Insert some values
INSERT INTO StarBreak (Name, DateLastPost)
	VALUES ('Denis','20130101'),('Ted','20121231'),('Koen','20121227'),('Jes','20121221');
GO
```
Now query the view again but use this query to make sure you only select the 3 existing columns:

```sql
SELECT ID, Name, DateLastPost FROM GetStarFromStarBreak
```

And the result is:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/StarPerform6.JPG?mtime=1357132583"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/StarPerform6.JPG?mtime=1357132583" width="672" height="66" /></a>
</div>

You see the code breaks because the view still expects 4 columns although you specified you only query 3 columns from the view.

**Conclusion**

We have seen several reasons to stay away from the SELECT * statement but we do understand the pain in typing in 127 column names. So here is a last tip:
  
When you drag the Columns folder of your table in Object Explorer to the Query Pane, SQL Server Management Studio will automatically list the column names separated with commas for you.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/StarPerform7.JPG?mtime=1357132592"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/StarPerform7.JPG?mtime=1357132592" width="608" height="233" /></a>
</div>

 [1]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/select-vs-select-1-with
 [2]: http://www.datamanipulation.net/sqlquerystress/