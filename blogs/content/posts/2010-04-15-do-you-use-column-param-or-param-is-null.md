---
title: 'Do you use  Column=@Param OR @Param IS NULL in your WHERE clause? Don’t, it doesn’t perform'
author: SQLDenis
type: post
date: 2010-04-15T09:55:44+00:00
ID: 711
excerpt: 'You see this kind of question all the time in newsgroups/forums, someone wants to return all the rows if nothing is passed in or just the rows that match the variable when something is passed in. Usually someone will reply with a suggestion to do someth&hellip;'
url: /index.php/datamgmt/datadesign/do-you-use-column-param-or-param-is-null/
views:
  - 71066
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
tags:
  - performance
  - pitfalls
  - sql server 2000
  - sql server 2005
  - sql server 2008
  - tip

---
You see this kind of question all the time in newsgroups/forums, someone wants to return all the rows if nothing is passed in or just the rows that match the variable when something is passed in. Usually someone will reply with a suggestion to do something like this

WHERE (SomeColumn=@col OR @col IS NULL)

The problem with that approach is that it doesn't perform well, let's take a look, first create this table

sql
USE tempdb
GO



CREATE TABLE Test(SomeCol1 INT NOT NULL, Somecol2 INT NOT NULL)

INSERT Test
SELECT number,low FROM master..spt_values
WHERE TYPE = 'p'


CREATE INDEX ix_test ON Test(Somecol2)
GO
```

Here is the query that uses the method described before, I am using AND 1=1 so that this query will match the one I will show later

sql
DECLARE @col INT
SELECT @col = 1

SELECT SomeCol2 
FROM Test
WHERE 1 =1
AND  (SomeCol2=@col OR @col IS NULL)


```
Here is the query using dynamic SQL

sql
GO

DECLARE @col INT
SELECT @col = 1

DECLARE @SQL NVARCHAR(4000)
    SET @SQL = 'SELECT SomeCol2 
				FROM Test
				WHERE 1 =1'

IF @col IS NOT NULL 
    SET @SQL = @SQL + ' AND SomeCol2=@InnerParamcol '
    
    
    
EXEC sp_executesql @SQL,N'@InnerParamcol INT',@col
```

Now let's run these queries and take a look at the reads

sql
SET STATISTICS IO ON
GO

DECLARE @col INT
SELECT @col = 1

SELECT SomeCol2 
FROM Test
WHERE 1 =1
AND  (SomeCol2=@col OR @col IS NULL)




GO

DECLARE @col INT
SELECT @col = 1

DECLARE @SQL NVARCHAR(4000)
    SET @SQL = 'SELECT SomeCol2 
				FROM Test
				WHERE 1 =1'

IF @col IS NOT NULL 
    SET @SQL = @SQL + ' AND SomeCol2=@InnerParamcol '
    
    
    
EXEC sp_executesql @SQL,N'@InnerParamcol INT',@col

SET STATISTICS IO OFF
GO
```

_(8 row(s) affected)
  
Table 'Test'. Scan count 1, logical reads 6, physical reads 0, read-ahead reads 0, lob logical reads 0, lob physical reads 0, lob read-ahead reads 0.</p> 

(8 row(s) affected)
  
Table 'Test'. Scan count 1, logical reads 2, physical reads 0, read-ahead reads 0, lob logical reads 0, lob physical reads 0, lob read-ahead reads 0.</em>

As you can see the dynamic SQL query only uses 2 reads where the other solution uses 6 reads.

Here is an image of the execution plan for both queries.

<img src="/wp-content/uploads/blogs/DataMgmt//Plan.PNG" alt="" title="" width="607" height="312" />

The execution plan show that the dynamic SQL is using a seek where the other query is using a scan

As you can see, there is a place for dynamic SQL and if you use it correctly you will also get plan reuse, take a look at the post [Changing exec to sp_executesql doesn't provide any benefit if you are not using parameters correctly][1] to find out how to use dynamic SQL correctly

**[EDIT]**

Someone on twitter suggested to try this query

sql
DECLARE @col INT
SELECT @col = 1
 
SELECT SomeCol2
FROM Test
WHERE 1 =1
AND  SomeCol2 = isnull(@col,SomeCol2)
```

That query also does an index scan.

Here is the execution plan in text for both of the queries that cause the scan

|–Index Scan(OBJECT:([tempdb].[dbo].[Test].[ix_test]),
   
WHERE:([tempdb].[dbo].[Test].[Somecol2]=isnull([@col],[tempdb].[dbo].[Test].[Somecol2])))

|–Index Scan(OBJECT:([tempdb].[dbo].[Test].[ix_test]),
    
WHERE:([tempdb].[dbo].[Test].[Somecol2]=[@col] OR [@col] IS NULL))

There was also a comment about recompiles, when you use sp_executesql you should not get recompiles when changing the value that you are passing in. I ran this query

sql
DECLARE @col INT
SELECT @col = 1
 
DECLARE @SQL NVARCHAR(4000)
    SET @SQL = 'SELECT SomeCol2
                FROM Test
                WHERE 1 =1'
 
IF @col IS NOT NULL
    SET @SQL = @SQL + ' AND SomeCol2=@InnerParamcol '
   EXEC SP_EXECUTESQL @SQL,N'@InnerParamcol INT',@col 
   Go
   
      
      
      DECLARE @col INT
SELECT @col = 2
 
DECLARE @SQL NVARCHAR(4000)
    SET @SQL = 'SELECT SomeCol2
                FROM Test
                WHERE 1 =1'
 
IF @col IS NOT NULL
    SET @SQL = @SQL + ' AND SomeCol2=@InnerParamcol '
   
   EXEC SP_EXECUTESQL @SQL,N'@InnerParamcol INT',@col 
   Go
   
   
   DECLARE @col INT
SELECT @col = 3
 
DECLARE @SQL NVARCHAR(4000)
    SET @SQL = 'SELECT SomeCol2
                FROM Test
                WHERE 1 =1'
 
IF @col IS NOT NULL
    SET @SQL = @SQL + ' AND SomeCol2=@InnerParamcol '
   
   EXEC SP_EXECUTESQL @SQL,N'@InnerParamcol INT',@col 
   Go
   
```

And then I ran a trace checking for SQL:StmtRecompile

<img src="/wp-content/uploads/blogs/DataMgmt//Profiler.PNG" alt="" title="" width="388" height="193" />

Here is the output from that trace

<img src="/wp-content/uploads/blogs/DataMgmt//trace.PNG" alt="" title="" width="501" height="578" />

**[/EDIT]**

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][2] forum or our [Microsoft SQL Server Admin][3] forum**<ins></ins>

 [1]: /index.php/DataMgmt/DataDesign/changing-exec-to-sp_executesql-doesn-t-p
 [2]: http://forum.ltd.local/viewforum.php?f=17
 [3]: http://forum.ltd.local/viewforum.php?f=22