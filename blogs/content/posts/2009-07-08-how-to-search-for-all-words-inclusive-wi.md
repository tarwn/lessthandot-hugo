---
title: How to search for all words inclusive without using Full Text search
author: Naomi Nosonovsky
type: post
date: 2009-07-09T00:19:54+00:00
ID: 499
url: /index.php/datamgmt/datadesign/how-to-search-for-all-words-inclusive-wi/
views:
  - 32494
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Microsoft SQL Server

---
I encountered this question last October in one of the forums I'm frequent. The question was: given the table with the keywords, find all records from some table which would include all these words (Implementing search with AND keyword). <!--more-->From the first glance the problem looks trivial, but for me the solution was not very obvious and I used Borislav Borissov's help in solving it.

I want to also note, that we can pass the list of keywords as a delimited list and split into table using any of the available splitting functions (you can explore this problem at [Arrays and Lists in SQL Server][1] blog)

The code bellow illustrates the problem and the solution I found.

sql
DECLARE @MyTable TABLE (Id int identity(1,1), Searched varchar(200))
DECLARE @Keys    TABLE (Word varchar(200), WordId int identity(1,1))

INSERT INTO @MyTable VALUES ('Mother Father Daughter Son')
INSERT INTO @MyTable VALUES ('Mother Daughter Son')
INSERT INTO @MyTable VALUES ('Mother Son')
INSERT INTO @MyTable VALUES ('Daughter Son')
INSERT INTO @MyTable VALUES ('Mother Father Son')
INSERT INTO @MyTable VALUES ('Son Daughter Father')
INSERT INTO @MyTable VALUES ('Mother Son')
INSERT INTO @MyTable VALUES ('Other Word')
INSERT INTO @MyTable VALUES ('Mother Father Daughter Brother Son')
INSERT INTO @MyTable VALUES ('Mother Daughter Son Stepdaughter')
INSERT INTO @MyTable VALUES ('Mother Son And Stepson and Daughter and Father and Grandfather')
INSERT INTO @MyTable VALUES ('Daughter Son Family')
INSERT INTO @MyTable VALUES ('Mother Brother Father Son Orphan')
INSERT INTO @MyTable VALUES ('Son or Daughter or Father')
INSERT INTO @MyTable VALUES ('Mother And Son')
INSERT INTO @MyTable VALUES ('Other Word One More')

INSERT INTO @Keys VALUES ('Mother')
INSERT INTO @Keys VALUES ('Father')
INSERT INTO @Keys VALUES ('Son')
INSERT INTO @Keys VALUES ('Daughter')

DECLARE @nAllWords int
SELECT @nAllWords = COUNT(*) FROM @Keys

SELECT MyTable.*
FROM @MyTable MyTable
INNER JOIN (SELECT MyTable.Id
                   FROM @MyTable MyTable
            INNER JOIN @Keys KeyWords ON ' ' + MyTable.Searched + ' ' LIKE '% ' + KeyWords.Word  + ' %'
            GROUP BY MyTable.Id
            HAVING COUNT(Distinct(KeyWords.Word)) = @nAllWords) Tbl1 ON MyTable.Id = Tbl1.Id
```

The above is the original test case and the solution we implemented with Borislav.

Based on Nikola's comments I re-worked the test case. The last suggestion performs the best. We can get further improvement if we include '% ' in our Keys and WordsToExclude tables. For the explanation of the last Nikola's query see http://msdn.microsoft.com/en-us/library/ms178543.aspx

See also this blog [ALL, ANY and SOME: The Three Stooges][2] 

sql
USE [AllTests]
GO

/****** Object:  Table [dbo].[RealTest]    Script Date: 08/19/2009 19:39:24 ******/
SET ANSI_NULLS ON
GO

SET QUOTED_IDENTIFIER ON
GO

SET ANSI_PADDING ON
GO

IF NOT EXISTS (SELECT * FROM sys.objects WHERE object_id = OBJECT_ID(N'[dbo].[RealTest]') AND type in (N'U'))
BEGIN
CREATE TABLE [dbo].[RealTest](
	[Id] [int] IDENTITY(1,1) NOT NULL,
	[Searched] [varchar](200) NULL,
 CONSTRAINT [PK_RealTest] PRIMARY KEY CLUSTERED 
(
	[Id] ASC
)WITH (PAD_INDEX  = OFF, STATISTICS_NORECOMPUTE  = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS  = ON, ALLOW_PAGE_LOCKS  = ON) ON [PRIMARY]
) ON [PRIMARY]
END
GO

--ALTER TABLE [dbo].[RealTest] DISABLE CHANGE_TRACKING 
GO

SET ANSI_PADDING OFF
GO

USE [AllTests]
GO

USE [AllTests]
GO

/****** Object:  Index [PK_RealTest]    Script Date: 08/19/2009 19:53:06 ******/
IF NOT EXISTS (SELECT * FROM sys.indexes WHERE object_id = OBJECT_ID(N'[dbo].[RealTest]') AND name = N'PK_RealTest')
ALTER TABLE [dbo].[RealTest] ADD  CONSTRAINT [PK_RealTest] PRIMARY KEY CLUSTERED 
(
	[Id] ASC
)WITH (PAD_INDEX  = OFF, STATISTICS_NORECOMPUTE  = OFF, SORT_IN_TEMPDB = OFF, IGNORE_DUP_KEY = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS  = ON, ALLOW_PAGE_LOCKS  = ON) ON [PRIMARY]
GO


/****** Object:  Index [IX_RealTest]    Script Date: 08/19/2009 19:52:29 ******/
IF NOT EXISTS (SELECT * FROM sys.indexes WHERE object_id = OBJECT_ID(N'[dbo].[RealTest]') AND name = N'IX_RealTest')
CREATE NONCLUSTERED INDEX [IX_RealTest] ON [dbo].[RealTest] 
(
	[Searched] ASC
)WITH (PAD_INDEX  = OFF, STATISTICS_NORECOMPUTE  = OFF, SORT_IN_TEMPDB = OFF, IGNORE_DUP_KEY = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS  = ON, ALLOW_PAGE_LOCKS  = ON) ON [PRIMARY]
GO




DECLARE @Keys    TABLE (Word VARCHAR(200), WordId INT IDENTITY(1,1))
DECLARE @WordsToExclude TABLE (Word VARCHAR(200), WordID INT IDENTITY(1,1))
DECLARE @I INT = 1
SET NOCOUNT ON
select COUNT(*) from RealTest  
WHILE @I<10000 
BEGIN
INSERT INTO RealTest  VALUES ('Mother Father Daughter Son iteration' + CAST(@i AS VARCHAR(10)))
INSERT INTO RealTest VALUES ('Mother Daughter Son iteration' + CAST(@i AS VARCHAR(10)))
INSERT INTO RealTest VALUES ('Mother Son iteration' + CAST(@i AS VARCHAR(10)))
INSERT INTO RealTest VALUES ('Daughter Son')
INSERT INTO RealTest VALUES ('Mother Father Son')
INSERT INTO RealTest VALUES ('Son Daughter Father')
INSERT INTO RealTest VALUES ('Mother Son')
INSERT INTO RealTest VALUES ('Other Word')
INSERT INTO RealTest VALUES ('Mother Father Daughter Brother Son')
INSERT INTO RealTest VALUES ('Exclude Mother Father Daughter Brother Son Orphan')
INSERT INTO RealTest VALUES ('Exclude Mother Father Daughter Brother Son Orphan')
INSERT INTO RealTest VALUES ('MotherFatherDaughterBrotherSon')
INSERT INTO RealTest VALUES ('Exclude Mother Father Daughter Son Stepdaughter')
INSERT INTO RealTest VALUES ('Brother Mother Father Daughter Son Stepdaughter')
INSERT INTO RealTest VALUES ('Mother Son And Stepson and Daughter and Father and Grandfather')
INSERT INTO RealTest VALUES ('Daughter Son Family')
INSERT INTO RealTest VALUES ('Mother Brother Father Daughter Son Orphan')
INSERT INTO RealTest VALUES ('Son or Daughter or Father')
INSERT INTO RealTest VALUES ('Mother And Son')
INSERT INTO RealTest VALUES ('Other Word One More')
 
SET @i = @i +1
END
 
INSERT INTO @Keys VALUES ('Mother')
INSERT INTO @Keys VALUES ('Father')
INSERT INTO @Keys VALUES ('Son')
INSERT INTO @Keys VALUES ('Daughter')
 
INSERT INTO @WordsToExclude VALUES ('Exclude') 
INSERT INTO @WordsToExclude VALUES ('Stepdaughter') 
 
SELECT COUNT(*) FROM RealTest 
 
SET STATISTICS TIME ON 
DECLARE @nAllWords INT
SELECT @nAllWords = COUNT(*) FROM @Keys
 
SELECT MyTable.*
FROM RealTest MyTable
INNER JOIN (SELECT MyTable.Id
                   FROM RealTest MyTable
            INNER JOIN @Keys KeyWords ON ' ' + MyTable.Searched + ' ' LIKE '% ' + KeyWords.Word  + ' %'
            WHERE not exists (SELECT 1 FROM RealTest M INNER JOIN @WordsToExclude W ON ' ' + M.Searched + ' ' LIKE '%' + W.Word + ' %' and M.Id = MyTable.Id)
            GROUP BY MyTable.Id
            HAVING COUNT(DISTINCT(KeyWords.Word)) = @nAllWords) Tbl1 ON MyTable.Id = Tbl1.Id --order by MyTable.Id 
 
PRINT 'Regular INNER JOIN ' +  CAST(@@ROWCOUNT AS VARCHAR(10))
            
SELECT x.Id,
       x.Searched
  FROM ( SELECT m.Id,
                m.Searched, 
                (SELECT COUNT(*) 
                   FROM @Keys k 
                  WHERE ' '+ m.Searched+' ' like '% ' + k.Word + ' %'
                ) AS n, (SELECT COUNT(*) FROM @WordsToExclude W WHERE ' ' + m.Searched + ' ' like '% ' + W.Word + ' %') AS WE
           FROM RealTest m
       ) AS x
 WHERE n=@nAllWords and isnull(WE,0) = 0 --order by x.Id 
 
PRINT 'Subquery solution ' +  CAST(@@ROWCOUNT AS VARCHAR(10))        

SELECT x.Id,
       x.Searched
  FROM ( SELECT m.Id,
                m.Searched,
                ( SELECT COUNT(*)
                    FROM @Keys k
                   WHERE ' '+ m.Searched+' ' like '% ' + k.Word + ' %'
                ) AS n
           FROM RealTest m
          WHERE not exists ( SELECT *
                               FROM @WordsToExclude W
                              WHERE ' ' + m.Searched + ' ' like '% ' + W.Word + ' %'
                           )
       ) AS x
 WHERE n=@nAllWords

PRINT 'Optimized Nikola''s  solution #1 ' +  CAST(@@ROWCOUNT AS VARCHAR(10))        

SELECT m.Id, m.Searched
FROM RealTest m
WHERE not exists (SELECT * FROM @WordsToExclude W WHERE ' ' + m.Searched + ' ' like '% ' + W.Word + ' %')
and 1 = ALL ( SELECT Case When ' ' + m.Searched + ' ' like '% ' + k.Word + ' %' Then 1 Else 0 End FROM @Keys k)

PRINT 'Optimized Nikola''s  solution #2 ' +  CAST(@@ROWCOUNT AS VARCHAR(10))        

    
SET STATISTICS TIME OFF
```
with the result on my PC
  
<code class="codespan">SQL Server Execution Times:</p>
<p> SQL Server Execution Times:<br />
   CPU time = 6599 ms,  elapsed time = 9802 ms.<br />
Regular INNER JOIN 79992</p>
<p> SQL Server Execution Times:<br />
   CPU time = 4961 ms,  elapsed time = 6697 ms.<br />
Subquery solution 79992</p>
<p> SQL Server Execution Times:<br />
   CPU time = 5366 ms,  elapsed time = 6716 ms.<br />
Optimized Nikola's  solution #1 79992</p>
<p> SQL Server Execution Times:<br />
   CPU time = 2901 ms,  elapsed time = 3670 ms.<br />
Optimized Nikola's  solution #2 79992</p>
<p></code>

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][3] forum or our [Microsoft SQL Server Admin][4] forum**<ins></ins>

 [1]: http://www.sommarskog.se/arrays-in-sql.html
 [2]: http://bradsruminations.blogspot.com/2009/08/all-any-and-some-three-stooges.html
 [3]: http://forum.ltd.local/viewforum.php?f=17
 [4]: http://forum.ltd.local/viewforum.php?f=22