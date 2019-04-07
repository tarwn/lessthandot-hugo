---
title: Triggers affect on SQL System Variables
author: Kevin Conan
type: post
date: 2013-03-12T19:35:00+00:00
ID: 2030
excerpt: A while back I ran into an issue where a user was complaining about @@IDENTITY not always returning the value they expected.
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/triggers-affect-on-sql-system/
views:
  - 10355
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - rowcount
  - identity
  - sql
  - trigger

---
A while back I ran into an issue where a user was complaining about @@IDENTITY not always returning the value they expected. 

This was happening on a table that was setup using 3rd party cross database platform replication software. The software used triggers to capture the changes to the table to send them over to an Oracle table and a DB2 table. The issue was caused by the trigger inserting records into another table that also had an identity column and that was being returned instead of the one from the original insert.

I used to also have a debate with a fellow DBA about @@ROWCOUNT. I use it all the time and haven’t had any issues. But I also wanted to know if triggers could affect it.
  
Here is some sample code that illustrates how triggers affect @@IDENTITY, @@ROWCOUNT and also demonstrates SCOPE_IDENTITY().

sql
CREATE TABLE tblTest (col1 INT IDENTITY (1,1) NOT NULL, col2 INT NULL);

GO

CREATE TRIGGER tr_ins_tblTest
    ON tblTest
 AFTER INSERT
AS 
BEGIN
	DECLARE @int AS INT;

	SELECT @int	= col1
	  FROM inserted;

	INSERT INTO tblTest (col2) 
	SELECT @int;

	SELECT	 'Inside Trigger 1'	AS [Where]
			,@@ROWCOUNT			AS [RowCount]
			,@@IDENTITY			AS [Identity]
			,SCOPE_IDENTITY()	AS [Scope Identity];

	--really try to mess with @@ROWCOUNT
	INSERT INTO tblTest (col2)
	SELECT 10
	WHERE 1 = 2;

	SELECT	 'Inside Trigger 2'	AS [Where]
			,@@ROWCOUNT			AS [RowCount]
			,@@IDENTITY			AS [Identity]
			,SCOPE_IDENTITY()	AS [Scope Identity];
	
END;

GO

INSERT INTO tblTest (col2) VALUES ('10');

SELECT	 'Outside Trigger'	AS [Where]
		,@@ROWCOUNT			AS [RowCount]
		,@@IDENTITY			AS [Identity]
		,SCOPE_IDENTITY()	AS [Scope Identity];

GO

DROP TABLE tblTest;
```

Here is the output from the above code:

<div class="image_block">
  <a href="/wp-content/uploads/users/kconan/reccount.JPG?mtime=1363124071"><img alt="" src="/wp-content/uploads/users/kconan/reccount.JPG?mtime=1363124071" width="327" height="144" /></a>
</div>

So we can see that @@ROWCOUNT is not affected by the trigger. However we also see that @@IDENTITY can be affected by triggers and the way to get around it is to use SCOPE_IDENTITY() instead.