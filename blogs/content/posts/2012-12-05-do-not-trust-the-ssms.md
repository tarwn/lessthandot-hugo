---
title: 'SQL Advent 2012 Day 5: Do not trust the SSMS designers'
author: SQLDenis
type: post
date: 2012-12-05T12:54:00+00:00
excerpt: |
  Today we are going to look at why you need to be able to write your own DDL statements.
  
  Read the following two lines, have you ever answered that or has anyone every answered that when asked this question?
  
  Question: How do you add a primary key to a table?
  Answer: I click on the yellow key icon in SSMS!
url: /index.php/datamgmt/dbprogramming/do-not-trust-the-ssms/
views:
  - 8863
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - ddl
  - designers
  - sql advent 2012
  - sql server 2008
  - sql server 2008 r2
  - sql server 2012
  - ssms

---
This is day five of the [SQL Advent 2012 series][1] of blog posts. Today we are going to look at why you need to be able to write your own DDL statements.

Read the following two lines, have you ever answered that or has anyone every answered that when asked this question?

Question: How do you add a primary key to a table?
  
Answer: I click on the yellow key icon in SSMS!

Technically, yes, that will create a primary key on the table but what will happen when you do that? Let&#8217;s take a look at some examples.

First create this very simple table

<pre>CREATE TABLE TestInt(Col1 tinyint not null)</pre>

Now they users changed their mind and want to insert values that go beyond what a tinyint can hold. If you try to insert 300, you will get an error

<pre>INSERT TestInt VALUES(300)</pre>

Msg 220, Level 16, State 2, Line 2
  
Arithmetic overflow error for data type tinyint, value = 300.
  
The statement has been terminated.

No, problem, I will just change the data type

<pre>ALTER TABLE TestInt ALTER COLUMN Col1 int NOT NULL</pre>

But what if you use the SSMS designer by right clicking on the table, choosing design and then changing the data type from tinyint to int? Here is what SSMS will do behind the scenes for you

<pre>/* To prevent any potential data loss issues, you should review this script in detail before running it outside the context of the database designer.*/
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
CREATE TABLE dbo.Tmp_TestInt
	(
	Col1 int NULL
	)  ON [PRIMARY]
GO
ALTER TABLE dbo.Tmp_TestInt SET (LOCK_ESCALATION = TABLE)
GO
IF EXISTS(SELECT * FROM dbo.TestInt)
	 EXEC('INSERT INTO dbo.Tmp_TestInt (Col1)
		SELECT CONVERT(int, Col1) FROM dbo.TestInt WITH (HOLDLOCK TABLOCKX)')
GO
DROP TABLE dbo.TestInt
GO
EXECUTE sp_rename N'dbo.Tmp_TestInt', N'TestInt', 'OBJECT' 
GO
COMMIT</pre>

That is right, it will create a new table, dump all the rows into this table, drop the original table and then rename the table that was just created to match the orginal table. This is overkill.

What about adding some defaults to the table, if you use the SSMS table designer, it will just create those and you have no way to specify a name for the default.

Here is how to create a default with T-SQL, now you can specify a name and make sure it matches your shop&#8217;s naming convention

<pre>ALTER TABLE dbo.TestInt ADD CONSTRAINT
	DF_TestInt_Col1 DEFAULT 1 FOR Col1</pre>

About that yellow key icon, let&#8217;s add a primary key to our table, I can do the following with T-SQL, I can also make it non clustered if I want to

<pre>ALTER TABLE dbo.TestInt ADD CONSTRAINT
	PK_TestInt PRIMARY KEY CLUSTERED 
	(Col1)  ON [PRIMARY]</pre>

Click that yellow key icon and here is what happens behind the scenes, I have not found a way to make it non clustered from the designer

<pre>/* To prevent any potential data loss issues, you should review this script in detail before running it outside the context of the database designer.*/
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
ALTER TABLE dbo.TestInt
	DROP CONSTRAINT DF_TestInt_Col1
GO
CREATE TABLE dbo.Tmp_TestInt
	(
	Col1 int NOT NULL
	)  ON [PRIMARY]
GO
ALTER TABLE dbo.Tmp_TestInt SET (LOCK_ESCALATION = TABLE)
GO
ALTER TABLE dbo.Tmp_TestInt ADD CONSTRAINT
	DF_TestInt_Col1 DEFAULT ((1)) FOR Col1
GO
IF EXISTS(SELECT * FROM dbo.TestInt)
	 EXEC('INSERT INTO dbo.Tmp_TestInt (Col1)
		SELECT Col1 FROM dbo.TestInt WITH (HOLDLOCK TABLOCKX)')
GO
DROP TABLE dbo.TestInt
GO
EXECUTE sp_rename N'dbo.Tmp_TestInt', N'TestInt', 'OBJECT' 
GO
ALTER TABLE dbo.TestInt ADD CONSTRAINT
	PK_TestInt PRIMARY KEY CLUSTERED 
	(
	Col1
	) WITH( STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]

GO
COMMIT</pre>

You might ask yourself why you should care, all the tables are small, this is not a big issue. This might be true now, what if you start a new job and now you have to supply alter, delete and create scripts? Now you are in trouble. I used to do the same when I started, I used the designers for everything, I didn&#8217;t even know Query Analyzer existed when I started, I created and modified the stored procedures straight inside Enterprise Manager. Trying to modify a view that had a CASE statement in Enterprise Manager from the designer&#8230;.yeah good luck with that one&#8230;.you would get some error that it wasn&#8217;t supported, I believe it also injected TOP 100 PERCENT ORDER BY in the view as well

I don&#8217;t miss those days at all. Get to learn T-SQL and get to love it, you might suffer when you start but you will become a better database developer.

Aaron Bertrand also has a post that you should read about the designers: [Bad habits to kick : using the visual designers][2]

That is all for day five of the [SQL Advent 2012 series][1], come back tomorrow for the next one, you can also check out all the posts from last year here: [SQL Advent 2011 Recap][3]

 [1]: /index.php/DataMgmt/DBProgramming/sql-advent-2012-here-is
 [2]: http://sqlblog.com/blogs/aaron_bertrand/archive/2009/10/14/bad-habits-to-kick-using-the-visual-designers.aspx
 [3]: /index.php/DataMgmt/DataDesign/sql-advent-2011-recap