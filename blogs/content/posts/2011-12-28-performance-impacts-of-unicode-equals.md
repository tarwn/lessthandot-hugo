---
title: Performance Impacts of Unicode, Equals vs LIKE, and Partially Filled Fixed Width
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2011-12-28T11:45:00+00:00
ID: 1466
excerpt: Several weeks ago I was refreshing my memory on some nvarchar/varchar tradeoffs when I ran into a post by Michael J Swart where he shared the results of investigating a performance problem in one of his live environments. After seeing the differences he posted, I was curious to see what the impact would be to fixed width types, partially populated fixed width columns, and performing equality instead of LIKE comparisons.
url: /index.php/datamgmt/dbprogramming/mssqlserver/performance-impacts-of-unicode-equals/
views:
  - 7689
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server

---
Several weeks ago I was refreshing my memory on some nvarchar/varchar tradeoffs when I ran into a post by Michael J Swart ([blog][1]|[twitter][2]) where he [shared the results][3] of investigating a performance problem in one of his live environments. After changing many columns from varchar to nvarchar, he had several procedures that were taking much longer to execute without any changes in I/O profile. This led him to compare the execution times of searching a varchar vs an nvarchar column, where he found that searching an nvarchar took 800% as long as a varchar.

After reading this, I was curious. Does that follow with char and nchar also? How about looking for exact matches (equals) instead of a search (LIKE)? And partially filled char fields are supposed to be poorer performing too, right?

## Results

The results are based on 4 runs of each query against 1,000,000 rows with explicitly limited parallelism. The name identifies the type, which operation was used (equals or LIKE), and the size of the field. All data was 36 characters, so “(36)” tests are fully populated values and “(72)” tests are half-full values.

<div style="font-size: 80%; color: #666666; text-align: center">
  <img src="http://tiernok.com/LTDBlog/nchar_all.png" title="Graph of all Results" /><br /> All Results, Normalized to Shortest Value (Char 32 &#8211; Equals)
</div>

This graph shows the average for each type as a percentage of the smallest value, &#8216;Char(72) &#8211; Equals'. What's immediately obvious is the level of difference between an equals operation and a LIKE, especially when we get to the trailing, partially filed NCHAR field (wow!).

The data was pretty consistent across the 4 runs used for these graphs:

<table class="zebra">
  <tr>
    <th>
      Test
    </th>
    
    <th>
      Average (ms)
    </th>
    
    <th>
      StdDev (ms)
    </th>
  </tr>
  
  <tr>
    <td>
      Char(36) &#8211; Equals
    </td>
    
    <td>
      113
    </td>
    
    <td>
    </td>
  </tr>
  
  <tr>
    <td>
      Char(36) &#8211; Like
    </td>
    
    <td>
      596
    </td>
    
    <td>
    </td>
  </tr>
  
  <tr>
    <td>
      Char(72) &#8211; Equals
    </td>
    
    <td>
      117
    </td>
    
    <td>
      2
    </td>
  </tr>
  
  <tr>
    <td>
      Char(72) &#8211; Like
    </td>
    
    <td>
      1043
    </td>
    
    <td>
      1.5
    </td>
  </tr>
  
  <tr>
    <td>
      NChar(36) &#8211; Equals
    </td>
    
    <td>
      123
    </td>
    
    <td>
    </td>
  </tr>
  
  <tr>
    <td>
      NChar(36) &#8211; Like
    </td>
    
    <td>
      3951
    </td>
    
    <td>
      13.08
    </td>
  </tr>
  
  <tr>
    <td>
      NChar(72) &#8211; Equals
    </td>
    
    <td>
      121
    </td>
    
    <td>
      1.5
    </td>
  </tr>
  
  <tr>
    <td>
      NChar(72) &#8211; Like
    </td>
    
    <td>
      7350
    </td>
    
    <td>
    </td>
  </tr>
  
  <tr>
    <td>
      NVarchar(36) &#8211; Equals
    </td>
    
    <td>
      153
    </td>
    
    <td>
    </td>
  </tr>
  
  <tr>
    <td>
      NVarchar(36) &#8211; Like
    </td>
    
    <td>
      3997
    </td>
    
    <td>
      8.85
    </td>
  </tr>
  
  <tr>
    <td>
      NVarchar(72) &#8211; Equals
    </td>
    
    <td>
      147
    </td>
    
    <td>
      2
    </td>
  </tr>
  
  <tr>
    <td>
      NVarchar(72) &#8211; Like
    </td>
    
    <td>
      3972
    </td>
    
    <td>
      3
    </td>
  </tr>
  
  <tr>
    <td>
      Varchar(36) &#8211; Equals
    </td>
    
    <td>
      136
    </td>
    
    <td>
      2.87
    </td>
  </tr>
  
  <tr>
    <td>
      Varchar(36) &#8211; Like
    </td>
    
    <td>
      623
    </td>
    
    <td>
      2.45
    </td>
  </tr>
  
  <tr>
    <td>
      Varchar(72) &#8211; Equals
    </td>
    
    <td>
      141
    </td>
    
    <td>
      1.5
    </td>
  </tr>
  
  <tr>
    <td>
      Varchar(72) &#8211; Like
    </td>
    
    <td>
      630
    </td>
    
    <td>
    </td>
  </tr>
</table>

_Note: I originally had 5 runs, but removed the first since it had consistently higher values, with 2 cases far outside several standard devitations._

### Comparing EQUALS statements

There is limited statistical significance in the fixed-width EQUALs, but the values do group together by type. 

<div style="font-size: 80%; color: #666666; text-align: center">
  <img src="http://tiernok.com/LTDBlog/nchar_equals.png" title="Graph of Equals Results" /><br /> Equals Results, Normalized to Shortest Value (Char 32 &#8211; Equals)
</div>

It is interesting to note that there is a pretty consistent 20% gap between fixed and variable widths for the same types (char to varchar, for instance), regardless of whether the column is partially or fully populated.

### Comparing LIKE statements

There is a much broader impact when we start looking at the LIKE statement comparisons. Like Michael's post above, there is a noticeable difference (on my system, ~650%) between searching a varchar and an nvarchar column.

<div style="font-size: 80%; color: #666666; text-align: center">
  <img src="http://tiernok.com/LTDBlog/nchar_like.png" title="Graph of LIKE Results" /><br /> LIKE Results, Normalized to Shortest Value (Char 32 &#8211; Like)
</div>

The conclusions I see are:

  * The previously mentioned difference between unicode and non-unicode fields is present
  * Fixed width fields suffer the same performance impact as variable length
  * Partially populated fixed width fields ran 75-90% longer than the associated fully populated column
  * Unicode fields were consistently 6x slower (on my system) than their non-unicode variants

Poorly sized nchar seems to be the real winner.

## Scripts

The script for these results was heavily based on the one in Michael's post above. The prep script assumes you have a numbers table ([here is a script for one][4]).

**Setup Sample Data Table**

sql
-----------------------------------
-- Prep - Generate Sample Data

IF EXISTS (SELECT 1 FROM INFORMATION_SCHEMA.TABLES WHERE TABLE_SCHEMA = 'dbo' AND TABLE_NAME = 'CharTypeTest')
DROP TABLE dbo.CharTypeTest;

CREATE TABLE dbo.CharTypeTest(
	CharTypeTestId int identity(1,1) NOT NULL,
	VarcharField36 varchar(36) NOT NULL,
	VarcharField72 varchar(72) NOT NULL,
	CharField36 char(36) NOT NULL,
	CharField72 char(72) NOT NULL,
	NVarcharField36 nvarchar(36) NOT NULL,
	NVarcharField72 nvarchar(72) NOT NULL,
	NCharField36 nchar(36) NOT NULL,
	NCharField72 nchar(72) NOT NULL	);

WITH SampleData AS(
      SELECT CAST(NEWID() as CHAR(36)) AS SampleText
      FROM Numbers N1, Numbers N2           -- 50,000 * 20 = 1,000,000
      WHERE N1.Number < 50000 AND N2.Number < 20
)


INSERT INTO dbo.CharTypeTest(VarcharField36, CharField36, NvarcharField36, NCharField36,
                             VarcharField72, CharField72, NvarcharField72, NCharField72)
SELECT SampleText, SampleText, SampleText, SampleText, SampleText, SampleText, SampleText, SampleText
FROM SampleData;
```
And here is the test code:

**Setup Sample Data Table**

sql
-----------------------------
-- Test

CREATE TABLE #Results (name varchar(30), elapsed int, run int);
DECLARE @StartTime DateTime, @run int;
SELECT @run = 1;

WHILE (SELECT COUNT(1) FROM #Results WHERE name = 'Char(36) - Equals') < 2
BEGIN

	SET @StartTime = GETDATE();
	SELECT COUNT(1) FROM CharTypeTest WHERE CharField36 = 'abcd' OPTION (MAXDOP 1);
	INSERT INTO #Results(name,elapsed,run) VALUES('Char(36) - Equals', DateDiff(ms, @StartTime, GETDATE()),@run);

	SET @StartTime = GETDATE();
	SELECT COUNT(1) FROM CharTypeTest WHERE VarcharField36 = 'abcd' OPTION (MAXDOP 1);
	INSERT INTO #Results(name,elapsed,run) VALUES('Varchar(36) - Equals', DateDiff(ms, @StartTime, GETDATE()),@run);

	SET @StartTime = GETDATE();
	SELECT COUNT(1) FROM CharTypeTest WHERE NCharField36 = N'abcd' OPTION (MAXDOP 1);
	INSERT INTO #Results(name,elapsed,run) VALUES('NChar(36) - Equals', DateDiff(ms, @StartTime, GETDATE()),@run);

	SET @StartTime = GETDATE();
	SELECT COUNT(1) FROM CharTypeTest WHERE NVarcharField36 = N'abcd' OPTION (MAXDOP 1);
	INSERT INTO #Results(name,elapsed,run) VALUES('NVarchar(36) - Equals', DateDiff(ms, @StartTime, GETDATE()),@run);

	SET @StartTime = GETDATE();
	SELECT COUNT(1) FROM CharTypeTest WHERE CharField36 LIKE '%abcd%' OPTION (MAXDOP 1);
	INSERT INTO #Results(name,elapsed,run) VALUES('Char(36) - Like', DateDiff(ms, @StartTime, GETDATE()),@run);

	SET @StartTime = GETDATE();
	SELECT COUNT(1) FROM CharTypeTest WHERE VarcharField36 LIKE '%abcd%' OPTION (MAXDOP 1);
	INSERT INTO #Results(name,elapsed,run) VALUES('Varchar(36) - Like', DateDiff(ms, @StartTime, GETDATE()),@run);

	SET @StartTime = GETDATE();
	SELECT COUNT(1) FROM CharTypeTest WHERE NCharField36 LIKE N'%abcd%' OPTION (MAXDOP 1);
	INSERT INTO #Results(name,elapsed,run) VALUES('NChar(36) - Like', DateDiff(ms, @StartTime, GETDATE()),@run);

	SET @StartTime = GETDATE();
	SELECT COUNT(1) FROM CharTypeTest WHERE NVarcharField36 LIKE N'%abcd%' OPTION (MAXDOP 1);
	INSERT INTO #Results(name,elapsed,run) VALUES('NVarchar(36) - Like', DateDiff(ms, @StartTime, GETDATE()),@run);
	 
	SET @StartTime = GETDATE();
	SELECT COUNT(1) FROM CharTypeTest WHERE CharField72 = 'abcd' OPTION (MAXDOP 1);
	INSERT INTO #Results(name,elapsed,run) VALUES('Char(72) - Equals', DateDiff(ms, @StartTime, GETDATE()),@run);

	SET @StartTime = GETDATE();
	SELECT COUNT(1) FROM CharTypeTest WHERE VarcharField72 = 'abcd' OPTION (MAXDOP 1);
	INSERT INTO #Results(name,elapsed,run) VALUES('Varchar(72) - Equals', DateDiff(ms, @StartTime, GETDATE()),@run);

	SET @StartTime = GETDATE();
	SELECT COUNT(1) FROM CharTypeTest WHERE NCharField72 = N'abcd' OPTION (MAXDOP 1);
	INSERT INTO #Results(name,elapsed,run) VALUES('NChar(72) - Equals', DateDiff(ms, @StartTime, GETDATE()),@run);

	SET @StartTime = GETDATE();
	SELECT COUNT(1) FROM CharTypeTest WHERE NVarcharField72 = N'abcd' OPTION (MAXDOP 1);
	INSERT INTO #Results(name,elapsed,run) VALUES('NVarchar(72) - Equals', DateDiff(ms, @StartTime, GETDATE()),@run);

	SET @StartTime = GETDATE();
	SELECT COUNT(1) FROM CharTypeTest WHERE CharField72 LIKE '%abcd%' OPTION (MAXDOP 1);
	INSERT INTO #Results(name,elapsed,run) VALUES('Char(72) - Like', DateDiff(ms, @StartTime, GETDATE()),@run);

	SET @StartTime = GETDATE();
	SELECT COUNT(1) FROM CharTypeTest WHERE VarcharField72 LIKE '%abcd%' OPTION (MAXDOP 1);
	INSERT INTO #Results(name,elapsed,run) VALUES('Varchar(72) - Like', DateDiff(ms, @StartTime, GETDATE()),@run);

	SET @StartTime = GETDATE();
	SELECT COUNT(1) FROM CharTypeTest WHERE NCharField72 LIKE N'%abcd%' OPTION (MAXDOP 1);
	INSERT INTO #Results(name,elapsed,run) VALUES('NChar(72) - Like', DateDiff(ms, @StartTime, GETDATE()),@run);

	SET @StartTime = GETDATE();
	SELECT COUNT(1) FROM CharTypeTest WHERE NVarcharField72 LIKE N'%abcd%' OPTION (MAXDOP 1);
	INSERT INTO #Results(name,elapsed,run) VALUES('NVarchar(72) - Like', DateDiff(ms, @StartTime, GETDATE()),@run);
	
	SELECT @run = @run + 1;
END; 

SELECT * FROM #Results;

DROP TABLE #Results;
```
I realize this is not a groundbreaking post, but after finding the results out for myself I thought it would be interesting to share. It also underlines and italicizes the need to fit your data definitions to the data you will be storing.

 [1]: http://michaeljswart.com/ "Michael J Swart's Blog"
 [2]: https://twitter.com/#!/MJSwart "@MJSwart on twitter"
 [3]: http://michaeljswart.com/2011/02/searching-inside-strings-cpu-is-eight-times-worse-for-unicode-strings/ "Searching Inside Strings: CPU is Eight Times Worse For Unicode Strings"
 [4]: http://sqlblog.com/blogs/adam_machanic/archive/2006/07/12/you-require-a-numbers-table.aspx "You Require a Numbers Table by Adam Machanic"