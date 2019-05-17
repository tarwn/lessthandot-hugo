---
title: Match your defaults with your trigger values to prevent horrible fragmentation on your indexes and tables
author: SQLDenis
type: post
date: 2011-05-06T13:47:00+00:00
ID: 1160
excerpt: "Here is a quick demonstration that shows you what can happen when you use defaults that are much shorter than the value that is updated from an insert trigger. The ModifiedBy  column has a default of '' but in the trigger it gets updated to 'Someapplica&hellip;"
url: /index.php/datamgmt/datadesign/match-your-defaults-with-your/
views:
  - 4647
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - pitfalls
  - sql server 2000
  - sql server 2005
  - sql server 2008
  - triggers

---
Here is a quick demonstration that shows you what can happen when you use defaults that are much shorter than the value that is updated from an insert trigger. The ModifiedBy column has a default of " but in the trigger it gets updated to 'SomeapplicationName used by ' +SUSER\_NAME(). Ideally you want the default to also be 'SomeapplicationName used by ' +SUSER\_NAME()

Let's take a look, first create the following table

```sql
CREATE TABLE TestFrag (
	id INT NOT NULL IDENTITY PRIMARY KEY, 
	SomeData UNIQUEIDENTIFIER DEFAULT newsequentialid(),
	Somedate datetime DEFAULT GETDATE(),
	SomeOtherData CHAR(200) DEFAULT 'bla',
	ModifiedBy varchar(100) DEFAULT '')
```
Add a trigger on that table

```sql
CREATE TRIGGER [dbo].[Tr_TestFrag] ON [dbo].[TestFrag] FOR Insert 
AS
UPDATE t
        SET    ModifiedBy = 'SomeapplicationName used by ' +SUSER_NAME()
        FROM   TestFrag t
        JOIN inserted i ON t.id = i.id
       
GO
```

Now add another identical table, the only difference is that the default matches what gets updated from within the trigger

```sql
CREATE TABLE TestFrag2 (
	id INT NOT NULL IDENTITY PRIMARY KEY, 
	SomeData UNIQUEIDENTIFIER DEFAULT newsequentialid(),
	Somedate datetime DEFAULT GETDATE(),
	SomeOtherData CHAR(200) DEFAULT 'bla',
	ModifiedBy varchar(100) DEFAULT 'SomeapplicationName used by ' +SUSER_NAME())
```

Here is the other trigger

```sql
CREATE TRIGGER [dbo].[Tr_TestFrag2] ON [dbo].[TestFrag2] FOR Insert 
AS
UPDATE t
        SET    ModifiedBy = 'SomeapplicationName used by ' +SUSER_NAME()
        FROM   TestFrag2 t
        JOIN inserted i ON t.id = i.id
       
GO
```

Now let's pump 100000 rows into both tables

```sql
INSERT TestFrag(Somedate)
SELECT TOP 100000 GETDATE() 
FROM sysobjects s1
CROSS JOIN sysobjects s2
CROSS JOIN sysobjects s3
```

```sql
INSERT TestFrag2(Somedate)
SELECT TOP 100000 GETDATE() 
FROM sysobjects s1
CROSS JOIN sysobjects s2
CROSS JOIN sysobjects s3
```

Run this to just verify that we have the same data

```sql
SELECT TOP 10 * FROM TestFrag
SELECT TOP 10 * FROM TestFrag2
```

Now let's look how much space each table is using

```sql
EXEC sp_spaceused 'TestFrag'
EXEC sp_spaceused 'TestFrag2'
```



<pre>name		rows		reserved	data		index_size	unused
TestFrag	100000     	47240 KB	47064 KB	104 KB		72 KB
TestFrag2	100000     	28744 KB	28576 KB	112 KB		56 KB</pre>

See that, the table with the same default as in the trigger is using a lot less data?

Now let's see how bad the fragmentation is. Here is the 2005 and up version

```sql
SELECT  
    OBJECT_NAME(i.OBJECT_ID) AS TableName
    ,indexstats.index_id
    ,index_type_desc
    ,avg_fragmentation_in_percent
    ,page_count
    ,record_count
    ,fill_factor
FROM    
sys.dm_db_index_physical_stats(DB_ID(), NULL, NULL, NULL, 'DETAILED') indexstats
INNER JOIN sys.indexes i ON i.OBJECT_ID = indexstats.OBJECT_ID
AND i.index_id = indexstats.index_id
WHERE OBJECT_NAME(i.OBJECT_ID) in ('TestFrag','TestFrag2')
```



<div class="tables">
  <table>
    <tr>
      <th>
        TableName
      </th>
      
      <th>
        index_id
      </th>
      
      <th>
        index_type_desc
      </th>
      
      <th>
        avg_fragmentation_in_percent
      </th>
      
      <th>
        page_count
      </th>
      
      <th>
        record_count
      </th>
      
      <th>
        fill_factor
      </th>
    </tr>
    
    <tr>
      <td>
        TestFrag
      </td>
      
      <td>
        1
      </td>
      
      <td>
        CLUSTERED INDEX
      </td>
      
      <td>
        99.422063573007
      </td>
      
      <td>
        5883
      </td>
      
      <td>
        100000
      </td>
      
      <td>
      </td>
    </tr>
    
    <tr>
      <td>
        TestFrag
      </td>
      
      <td>
        1
      </td>
      
      <td>
        CLUSTERED INDEX
      </td>
      
      <td>
      </td>
      
      <td>
        11
      </td>
      
      <td>
        5883
      </td>
      
      <td>
      </td>
    </tr>
    
    <tr>
      <td>
        TestFrag
      </td>
      
      <td>
        1
      </td>
      
      <td>
        CLUSTERED INDEX
      </td>
      
      <td>
      </td>
      
      <td>
        1
      </td>
      
      <td>
        11
      </td>
      
      <td>
      </td>
    </tr>
    
    <tr>
      <td>
        TestFrag2
      </td>
      
      <td>
        1
      </td>
      
      <td>
        CLUSTERED INDEX
      </td>
      
      <td>
        0.335946248600224
      </td>
      
      <td>
        3572
      </td>
      
      <td>
        100000
      </td>
      
      <td>
      </td>
    </tr>
    
    <tr>
      <td>
        TestFrag2
      </td>
      
      <td>
        1
      </td>
      
      <td>
        CLUSTERED INDEX
      </td>
      
      <td>
      </td>
      
      <td>
        12
      </td>
      
      <td>
        3572
      </td>
      
      <td>
      </td>
    </tr>
    
    <tr>
      <td>
        TestFrag2
      </td>
      
      <td>
        1
      </td>
      
      <td>
        CLUSTERED INDEX
      </td>
      
      <td>
      </td>
      
      <td>
        1
      </td>
      
      <td>
        12
      </td>
      
      <td>
      </td>
    </tr>
  </table>
</div>

Yikes, do you see that, one table is completely fragmented.

Here is the 2000 version by using showcontig

<pre>DBCC SHOWCONTIG scanning 'TestFrag' table...
Table: 'TestFrag' (933578364); index ID: 1, database ID: 14
TABLE level scan performed.
- Pages Scanned................................: <strong>5883</strong>
- Extents Scanned..............................: 738
- Extent Switches..............................: 5879
- Avg. Pages per Extent........................: 8.0
- Scan Density [Best Count:Actual Count].......: 12.52% [736:5880]
- Logical Scan Fragmentation ..................: <strong>99.42%</strong>
- Extent Scan Fragmentation ...................: 0.27%
- Avg. Bytes Free per Page.....................: <strong>3234.5</strong>
- Avg. Page Density (full).....................: 60.04%
DBCC execution completed. If DBCC printed error messages, contact your system administrator.


DBCC SHOWCONTIG scanning 'TestFrag2' table...
Table: 'TestFrag2' (1205579333); index ID: 1, database ID: 14
TABLE level scan performed.
- Pages Scanned................................: <strong>3572</strong>
- Extents Scanned..............................: 450
- Extent Switches..............................: 449
- Avg. Pages per Extent........................: 7.9
- Scan Density [Best Count:Actual Count].......: 99.33% [447:450]
- Logical Scan Fragmentation ..................: <strong>0.34%</strong>
- Extent Scan Fragmentation ...................: 0.44%
- Avg. Bytes Free per Page.....................: <strong>89.3</strong>
- Avg. Page Density (full).....................: 98.90%
DBCC execution completed. If DBCC printed error messages, contact your system administrator.
</pre>

As you can see you will get horrible page splits if your trigger expands the row every time an insert happens.

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][2] forum**<ins></ins>

 [1]: http://forum.lessthandot.com/viewforum.php?f=17
 [2]: http://forum.lessthandot.com/viewforum.php?f=22