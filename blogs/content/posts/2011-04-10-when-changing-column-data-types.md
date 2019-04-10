---
title: When changing column data types use ALTER TABLE TableName ALTER Column syntax, don’t drop and recreate column
author: SQLDenis
type: post
date: 2011-04-10T22:27:00+00:00
ID: 1107
excerpt: |
  Someone asked how to change the data type from datetime to datetime2
  
  Someone else answered the following
  
  You could add the new column.
  
  UPDATE Table SET NewColumn = OldColumn
  
  delete the old column
  
  then rename the new column.
  
  This of cou&hellip;
url: /index.php/datamgmt/datadesign/when-changing-column-data-types/
views:
  - 26932
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - dates
  - datetime
  - datetime2
  - gotcha
  - sql server 2008

---
Someone asked how to [change the data type from datetime to datetime2][1]

Here is the question

> I have a SQL Server 2005 database with a datetime column. There is already data in the table but now the customer needs dates before 1753. So I decided to migrate the database to a SQL Server 2008 to use the datetime2 type.
> 
> However I can't just switch the type of the column from datetime to datetime2. Is there a way to do this conversion or do I have to reimport the data?
  
> Someone else answered the following

Here is an answer that this person got from someone

> You could add the new column.
> 
> UPDATE Table SET NewColumn = OldColumn
> 
> delete the old column
> 
> then rename the new column.

This of course is highly inefficient. Just imagine running that suggestion on a table with millions or billions of rows.

My approach would be to do this instead: _ALTER TABLE TableName ALTER COLUMN ColumnName datetime2_

Let's take a closer look at the T-SQL needed for this

First create the following table and insert one row

sql
USE tempdb
GO

CREATE TABLE Test(SomeDate DATETIME)
INSERT Test values ('20110410')

SELECT * FROM Test
GO
```

Output
  
—————–
  
2011-04-10 00:00:00.000

Datetime accepts dates in the range from January 1, 1753, through December 31, 9999. If you try to insert a value before January 1, 1753, it will fail
  
Run the code below which will try to insert January 1, 1600, to see the error

sql
INSERT Test values ('16000101')
```

_Msg 242, Level 16, State 3, Line 1
  
The conversion of a varchar data type to a datetime data type resulted in an out-of-range value._

Now we will change the column from datetime to datetime2.
  
The syntax looks like this

_ALTER TABLE <TableName> ALTER column <ColumnName> <New date type>_

For our table the syntax will be the following: _ALTER TABLE Test ALTER column SomeDate datetime2_
  
Run the code below to make that happen

sql
ALTER TABLE Test ALTER column SomeDate datetime2
GO
```
So now, if we try to insert January 1, 1600, it should succeeed

sql
INSERT Test values ('16000101')
```

Now, you can look what is in the table

sql
SELECT * FROM Test
GO
```

Output
  
—————————
  
2011-04-10 00:00:00.0000000
  
1600-01-01 00:00:00.0000000

Just be aware that if you are changing data types, make sure that what you change to can hold the current values. If you are changing from varchar to integer, make sure you only have values that can be converted to integers, the operation will fail if they can't be converted to integers.

## Conclusion

Learn the product and learn it well. Don't overly depend on the wizards in SSMS, and if you use SSMS, hit the Script button to see what kind of T-SQL SSMS generates.

The wizards are nice but sometimes they get it wrong. Here is the code that the wizard generates to change the column

sql
/* To prevent any potential data loss issues, you should review this script in detail before running it outside the context of the database designer.*/
BEGIN TRANSACTION
SET QUOTED_IDENTIFIER ON
SET ARITHABORT ON
SET NUMERIC_ROUNDABORT OFF
SET CONCAT_NULL_YIELDS_NULL ON
SET ANSI_NULLS ON
SET ANSI_PADDING ON
SET ANSI_WARNINGS ON
COMMIT
BEGIN TRANSACTION
GO
CREATE TABLE dbo.Tmp_Test
	(
	SomeDate datetime2(7) NULL
	)  ON [PRIMARY]
GO
ALTER TABLE dbo.Tmp_Test SET (LOCK_ESCALATION = TABLE)
GO
IF EXISTS(SELECT * FROM dbo.Test)
	 EXEC('INSERT INTO dbo.Tmp_Test (SomeDate)
		SELECT CONVERT(datetime2(7), SomeDate) FROM dbo.Test WITH (HOLDLOCK TABLOCKX)')
GO
DROP TABLE dbo.Test
GO
EXECUTE sp_rename N'dbo.Tmp_Test', N'Test', 'OBJECT' 
GO
COMMIT

```
I definitely don't want that either, that creates a whole new table..yikes

Spend some time in Books On Line, maybe every day at lunch open a random topic and read it for half and hour and run the code examples. If you commute, download the topic to your local device or hit the online version and study it. Another good way to learn is of course hitting the newsgroups where you will see top notch advice from SQL Server experts

The more you know, the better it will be for you and your employer, if suddenly you can prove that yes, we can do this and here is a better way I found, you will be rewarded sooner or later.

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][2] forum or our [Microsoft SQL Server Admin][3] forum**<ins></ins>

 [1]: http://stackoverflow.com/questions/5581639/convert-datetime-column-to-datetime2-column-in-sql-server/5581702#5581702
 [2]: http://forum.ltd.local/viewforum.php?f=17
 [3]: http://forum.ltd.local/viewforum.php?f=22