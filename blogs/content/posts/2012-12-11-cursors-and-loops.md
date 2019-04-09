---
title: 'SQL Advent 2012 Day 11: Cursors and loops'
author: SQLDenis
type: post
date: 2012-12-11T13:01:00+00:00
ID: 1846
excerpt: |
  This is day eleven of the SQL Advent 2012 series of blog posts. Today we are going to look at cursors and loops.
  
  
  Loops in triggers
  Loops in stored procedures
  Loops for maintenance purposes
  
  
    
  That is all for day eleven of the SQL Advent 201&hellip;
url: /index.php/datamgmt/dbprogramming/cursors-and-loops/
views:
  - 13211
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - cursors
  - loops
  - sql server 2005
  - sql server 2008
  - sql server 2012

---
This is day eleven of the [SQL Advent 2012 series][1] of blog posts. Today we are going to look at cursors and loops.

## Why do we hate those poor cursors?

Let's first see why people tend to use cursors. Let's say you come from a procedural language and this is the first time you are using SQL. In the procedural language you know how to traverse a list, you of course will look for something that is similar in SQL……..bingo!!! you found it…the almighty cursor….the crusher of allmost all SQL Server performance. You start using it, your code works, you are happy, life is good. Now a team member tells you that the cursor is evil and should never ever be used. You are confused, if a cursor is never to be used then why is it part of the language? Well you might say the same for the goto statement, this exists in SQL however Edsger Dijkstra's letter _Go To Statement Considered Harmful_ was published in the March 1968 Communications of the ACM.

The reason that cursors are evil is that they tend to be slower than a set based solution. Cursors are not needed for 99% of the cases. SQL is a set based language, it works best with sets of data not row by row processing, when you do something set based it will generally perform hundreds of times faster than using a cursor.

Take a look at this code

sql
CREATE FUNCTION dbo.cursorEnroll ()
    RETURNS INT AS
    BEGIN
        DECLARE @studentsEnrolled INT
        SET @studentsEnrolled = 0
        DECLARE myCursor CURSOR FOR
            SELECT enrollementID
                FROM courseEnrollment
        OPEN myCursor;
 
        FETCH NEXT FROM myCursor INTO @studentsEnrolled
 
        WHILE @@FETCH_STATUS = 0
            BEGIN
                SET @studentsEnrolled = @studentsEnrolled+1
                    FETCH NEXT FROM myCursor INTO @studentsEnrolled
            END;
        CLOSE myCursor
        RETURN @studentsEnrolled
 
    END;
```

That whole flawed cursor logic can be replaced with one line of T-SQL

sql
SELECT @studentsEnrolled = COUNT(*) FROM courseEnrollment
```

Which one do you think will perform faster?

## What is more evil than a cursor?

If cursors are evil, then what is more evil than a cursor? Nested cursors of course, especially three nested cursors. Here is an example of some horrible code where a cursor is not needed

sql
CREATE PROCEDURE [dbo].[sp_SomeMadeUpName]
AS

DECLARE @SomeDate DATETIME

SET @SomeDate =  CONVERT(CHAR(10),getDate(),112)

EXEC sp_createSomeLinkedServer @SomeDate,@SomeDate,12

DECLARE @sql NVARCHAR(2000), @SomeID VARCHAR(20)



DECLARE SomeCursor CURSOR 
FOR
SELECT DISTINCT SomeID
FROM SomeTable
WHERE getDate() BETWEEN SomeStart and SomeEnd


OPEN SomeCursor

FETCH NEXT FROM SomeCursor INTO @SomeID

WHILE @@FETCH_STATUS = 0

	BEGIN
	
	PRINT @SomeID
	
	SET @sql = ''
	SET @sql = @sql + N'DECLARE @Date DATETIME, @Value FLOAT' + char(13) + char(13)
	SET @sql = @sql + N'DECLARE curData CURSOR FOR' + char(13)
	SET @sql = @sql + N'SELECT * ' + char(13)
	SET @sql = @sql + N'FROM OPENQUERY(LinkedServerName,''SELECT Date,' + RTRIM(@SomeID) + ' FROM SomeTable'')' + char(13) + char(13)
	SET @sql = @sql + N'OPEN curData' + char(13) + char(13)
	SET @sql = @sql + N'FETCH NEXT FROM curData INTO @Date,@Value' + char(13)
	SET @sql = @sql + N'WHILE @@FETCH_STATUS = 0' + char(13)
	SET @sql = @sql + N'BEGIN' + char(13)
	SET @sql = @sql + N'INSERT INTO SomeTAble' + char(13)
	SET @sql = @sql + N'VALUES(''' + @SomeID + ''',@Date,@Value)' + char(13)
	SET @sql = @sql + N'FETCH NEXT FROM curData INTO @Date,@Value' + char(13)
	SET @sql = @sql + N'END' + char(13)
	SET @sql = @sql + N'CLOSE curData' + char(13) + char(13)
	SET @sql = @sql + N'DEALLOCATE curData'	

	PRINT @sql + char(13) + char(13)

	EXEC sp_ExecuteSQL @sql

	FETCH NEXT FROM SomeCursor INTO @SomeID

	END

CLOSE SomeCursor
DEALLOCATE SomeCursor
```

Why the need of looping over the list od IDs? Join with the linked server and do all this in 3 lines of code

## Replacing one evil with another

If you are using while loops instead of cursors then you really have not replaced the cursor at all, you are still not doing a set based operation, everything is still going row by row. Aaron Bertrand has a good post here, no need for me to repeat the same [Bad Habits to Kick : Thinking a WHILE loop isn't a CURSOR][2]

## Loops in triggers

Usually you will find a loop in a trigger that will call some sort of stored procedures that needs to perform some kind of task for each row affected by the trigger. In the [Triggers, what to do, what not to do][3] post I already explained how to handle this scenario

## Loops in stored procedures

Here is where you will find the biggest offenders. All you have to do to find the procs with cursors is run the following piece of code

sql
SELECT * FROM sys.procedures 
WHERE OBJECT_DEFINITION((object_id) )LIKE '%DECLARE%%cursor%'
```

For while loops, just change the &#8216;%DECLARE%%cursor%' part to &#8216;%while%'

Look at those procs and investigate if you can rewrite them using a SET based operation instead

## Loops for maintenance purposes

One of the few times I will use cursors or while loops is if I need to get information about tables or databases where I have to get information from a stored procedure.

Here is an example

sql
CREATE TABLE #tempSpSpaceUsed (TableName VARCHAR(100),
								Rows INT,
								Reserved VARCHAR(100),
								Data VARCHAR(100),
								IndexSize VARCHAR(100),
								Unused VARCHAR(100))
								
								
SELECT name, ROW_NUMBER() OVER(ORDER BY name) AS id
INTO #temp
FROM sys.tables 

DECLARE @TableName VARCHAR(300)
DECLARE @loopid INT = 1, @maxID int = (SELECT MAX(id) FROM #temp)
WHILE @loopid <= @maxID
BEGIN

SELECT @TableName = name FROM #temp WHERE id = @loopid
INSERT #tempSpSpaceUsed
	EXEC('EXEC sp_spaceused ''' + @TableName + '''')
SET @loopid +=1
END

SELECT * FROM #tempSpSpaceUsed
```
Of course this can be simplified as well, you can just run this instead of the while loop and run the output

sql
SELECT DISTINCT 'INSERT #tempSpSPaceUsed
EXEC sp_spaceused ''' + [name] + ''''
FROM sys.tables
```

## Are cursors always evil?

No, cursors have their purpose, see the section above. Also take a look at Adam Machanic's post [Running sums, redux][4] where you can actually see that a cursor is faster if you are doing a running count

## How to change from cursors to a set based operation

Take a look at our wiki, there is an article named [Cursors and How to Avoid Them][5]
  
It has the following sections
  
1 Row by row processing, the impact on performance
  
2 Typical Uses of a Cursor and Set-Based Replacements for Them
  
2.1 To perform an insert using the values clause
  
2.2 Using joins in updates, and deletes
  
2.3 The need to process in different tables depending on a value in the table
  
2.4 Using case for special treatment based on a the data in a field
  
2.5 The need to do either an update or an insert depending on whether the record exists in the table

Finally if you need more cursor goodness, take a look at these three posts which are also mentioned in the wiki article

[The Truth About Cursors: Part I][6]
  
[The Truth About Cursors: Part II][7]
  
[The Truth About Cursors: Part III][8]

That is all for day eleven of the [SQL Advent 2012 series][1], come back tomorrow for the next one, you can also check out all the posts from last year here: [SQL Advent 2011 Recap][9]

 [1]: /index.php/DataMgmt/DBProgramming/sql-advent-2012-here-is
 [2]: http://sqlblog.com/blogs/aaron_bertrand/archive/2012/01/26/the-fallacy-that-a-while-loop-isn-t-a-cursor.aspx
 [3]: /index.php/DataMgmt/DBProgramming/triggers-what-to-do-what
 [4]: http://sqlblog.com/blogs/adam_machanic/archive/2006/07/12/running-sums-redux.aspx
 [5]: http://wiki.ltd.local/index.php/Cursors_and_How_to_Avoid_Them
 [6]: http://bradsruminations.blogspot.com/2010/05/truth-about-cursors-part-1.html
 [7]: http://bradsruminations.blogspot.com/2010/05/truth-about-cursors-part-2.html
 [8]: http://bradsruminations.blogspot.com/2010/05/truth-about-cursors-part-3.html
 [9]: /index.php/DataMgmt/DataDesign/sql-advent-2011-recap