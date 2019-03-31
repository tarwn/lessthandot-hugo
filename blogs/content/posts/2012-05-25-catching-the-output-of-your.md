---
title: Catching the OUTPUT of your DML statements
author: Axel Achten (axel8s)
type: post
date: 2012-05-25T12:04:00+00:00
excerpt: 'Suppose you need to log the old and new version of the data you change in a table in your database. If you ask a DBA how this could be done, I guess 80% will tell you to do it with an after trigger (the number is going down because every new edition of&hellip;'
url: /index.php/datamgmt/dbprogramming/mssqlserver/catching-the-output-of-your/
views:
  - 5673
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server
tags:
  - audit
  - log
  - output clause

---
Suppose you need to log the old and new version of the data you change in a table in your database. If you ask a DBA how this could be done, I guess 80% will tell you to do it with an after trigger (the number is going down because every new edition of SQL Server comes with new features to do this). If you ask a DBA what he thinks of triggers, 95% will tell you to avoid them as much as possible&#8230; So what should you do? Well it all depends on the requirements you have and how you&#8217;re data is saved. If you need to be sure that all changes are logged, also the direct changes not coming from a business application you&#8217;d better be looking at triggers and/or audits. If you just want to log from within your application you can consume the OUTPUT directly from your INSERT, UPDATE, DELETE or MERGE statement. Let&#8217;s see how it works. First of all gather some data to work with; I&#8217;ll use some data from the AdventureWorks database:

<pre>SELECT TOP (10) ProductID, Name, ListPrice, ModifiedDate
INTO ProductPrice
FROM Production.Product
WHERE MakeFlag = 1
	AND ListPrice &gt; 0</pre>

We also need a table to hold the old and new versions of the data and I also want to store who changed the data:

<pre>CREATE TABLE PriceLog (
	ProductID int,
	OldListPrice money,
	NewListPrice money,
	OldModifiedDate datetime,
	NewModifiedDate datetime,
	ModifyingUser varchar(20))</pre>

Now the data and logtable are in place we can start using the OUTPUT clause. The OUTPUT clause is used directly after the DML query and uses the DELETED and INSERTED prefixes to get the old and the new version of the data. Note that there is no UPDATED prefix. An update puts the original data in the DELETED, and the new values in the INSERTED OUTPUT. So this is how an update query will look:

<pre>UPDATE ProductPrice
SET ListPrice = ListPrice * 1.1, 
	ModifiedDate = GETDATE()
OUTPUT deleted.ProductID,
	deleted.ListPrice,
	inserted.ListPrice,
	deleted.ModifiedDate,
	inserted.ModifiedDate,
	SYSTEM_USER
	INTO PriceLog
WHERE ProductID = 517</pre>

When we look at the data in the PriceLog we see all the requested data:

<div class="image_block">
  <a href="/wp-content/uploads/users/axel8s/OUTPUT1.png?mtime=1337954208"><img alt="" src="/wp-content/uploads/users/axel8s/OUTPUT1.png?mtime=1337954208" width="622" height="63" /></a>
</div>

Now we can wrap the code in a stored procedure:

<pre>CREATE PROCEDURE PriceUpdate
	@ProductID int,
	@NewPrice money
AS
	SET NOCOUNT ON;
	UPDATE ProductPrice
	SET ListPrice = @NewPrice,
		ModifiedDate = GETDATE()
	OUTPUT deleted.ProductID,
		deleted.ListPrice,
		inserted.ListPrice,
		deleted.ModifiedDate,
		inserted.ModifiedDate,
		SYSTEM_USER
	INTO PriceLog
	WHERE ProductID = @ProductID
GO</pre>

This time we update a price using the stored procedure:

<pre>Exec PriceUpdate 520, 175.83</pre>

And check to see if the change is stored in the PriceLog table:

<div class="image_block">
  <a href="/wp-content/uploads/users/axel8s/OUTPUT2.png?mtime=1337954220"><img alt="" src="/wp-content/uploads/users/axel8s/OUTPUT2.png?mtime=1337954220" width="621" height="84" /></a>
</div>

So as you can see, using the OUTPUT clause can be a very effective way of logging changes to the data. The biggest disadvantage is when somebody is updating the data with a direct query. But in a production system this shouldn&#8217;t be the case.