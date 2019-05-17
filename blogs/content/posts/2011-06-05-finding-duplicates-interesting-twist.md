---
title: Finding Duplicates – Interesting twist
author: Naomi Nosonovsky
type: post
date: 2011-06-05T23:48:00+00:00
ID: 1203
excerpt: |
  A recent MSDN thread
  presented a very interesting problem - find duplicates based on any 4 of the 5 columns and eliminate the duplicates.
  
  Here is the data table we will be working with:
  
  
  CREATE TABLE tblTEST ( 
    ID           INT    UNIQUE    N&hellip;
url: /index.php/datamgmt/datadesign/finding-duplicates-interesting-twist/
views:
  - 5401
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
A recent [MSDN thread][1] presented a very interesting problem – find duplicates based on any 4 of the 5 columns and eliminate the duplicates.

Here is the data table we will be working with:

```sql
CREATE TABLE tblTEST ( 
  ID           INT    UNIQUE    NOT NULL, 
  FirstColumn  VARCHAR(50)    NOT NULL, 
  SecondColumn VARCHAR(50)    NOT NULL, 
  ThirdColumn  VARCHAR(50)    NOT NULL, 
  FourthColumn VARCHAR(50)    NOT NULL, 
  FifthColumn  VARCHAR(50)    NOT NULL ) 

INSERT INTO tblTEST 
           (ID, 
            FirstColumn, 
            SecondColumn, 
            ThirdColumn, 
            FourthColumn, 
            FifthColumn) 
VALUES     (1,'value1','value2','value3','value4','value5'), 
           (2,'value2','value3','value4','value5','value6'), 
           (3,'value1','value4','value5','value8','value9'), 
           (4,'value1','value2','value7','value8','value9'), 
           (5,'value3','value4','value5','value6','value7'), 
           (6,'value1','value2','value9','value4','value5'), 
           (7,'value2','value3','value4','value5','value11'), 
           (8,'value4','value5','value3','value9','value1')
```
Our test table:

<div class="tables">
  <table border="1" cellpadding ="5" cellspacing = "5"> <caption style="color:blue;font-weight:bold">Test Table with Duplicates</caption> 
  
  <tr>
    <th>
      ID
    </th>
    
    <th>
      FirstColumn
    </th>
    
    <th>
      SecondColumn
    </th>
    
    <th>
      ThirdColumn
    </th>
    
    <th>
      FourthColumn
    </th>
    
    <th>
      FifthColumn
    </th>
  </tr>
  
  <tr>
    <td>
      1
    </td>
    
    <td>
      value1
    </td>
    
    <td>
      value2
    </td>
    
    <td>
      value3
    </td>
    
    <td>
      value4
    </td>
    
    <td>
      value5
    </td>
  </tr>
  
  <tr>
    <td>
      2
    </td>
    
    <td>
      value2
    </td>
    
    <td>
      value3
    </td>
    
    <td>
      value4
    </td>
    
    <td>
      value5
    </td>
    
    <td>
      value6
    </td>
  </tr>
  
  <tr>
    <td>
      3
    </td>
    
    <td>
      value1
    </td>
    
    <td>
      value4
    </td>
    
    <td>
      value5
    </td>
    
    <td>
      value8
    </td>
    
    <td>
      value9
    </td>
  </tr>
  
  <tr>
    <td>
      4
    </td>
    
    <td>
      value1
    </td>
    
    <td>
      value2
    </td>
    
    <td>
      value7
    </td>
    
    <td>
      value8
    </td>
    
    <td>
      value9
    </td>
  </tr>
  
  <tr>
    <td>
      5
    </td>
    
    <td>
      value3
    </td>
    
    <td>
      value4
    </td>
    
    <td>
      value5
    </td>
    
    <td>
      value6
    </td>
    
    <td>
      value7
    </td>
  </tr>
  
  <tr>
    <td>
      6
    </td>
    
    <td>
      value1
    </td>
    
    <td>
      value2
    </td>
    
    <td>
      value9
    </td>
    
    <td>
      value4
    </td>
    
    <td>
      value5
    </td>
  </tr>
  
  <tr>
    <td>
      7
    </td>
    
    <td>
      value2
    </td>
    
    <td>
      value3
    </td>
    
    <td>
      value4
    </td>
    
    <td>
      value5
    </td>
    
    <td>
      value11
    </td>
  </tr>
  
  <tr>
    <td>
      8
    </td>
    
    <td>
      value4
    </td>
    
    <td>
      value5
    </td>
    
    <td>
      value3
    </td>
    
    <td>
      value9
    </td>
    
    <td>
      value1
    </td>
  </tr></table>
</div>

This problem is different from the typical find the duplicates problem and at first does not even seem solvable.

The first idea that comes to mind is to UNPIVOT values from these 5 columns into rows. This is simple enough with this code:

```sql
;with UnPvt AS (SELECT ID, ColValue FROM tblTEST 
UNPIVOT (ColValue FOR ColName IN ([FirstColumn],[SecondColumn],[ThirdColumn],[FourthColumn], [FifthColumn])) unpvt),
```
The second step is also more or less clear – find possible duplicates by counting IDs partitioned by ColValue

```sql
SameVals as (SELECT * FROM (select *, COUNT(ID) OVER (PARTITION by ColValue) as cntSame from UnPvt) X WHERE cntSame >=2),
```

Now, what can we do next? The next step was a road block for me. But then, an Eureka moment – we can use CROSS APPLY and find all records that have more than 4 values matching the current record values

```sql
DupRecs as (select T.*,S.cntDups, S.ID as DupID
 from tblTEST T CROSS APPLY (select S.ID, COUNT(*) as cntDups from SameVals S
WHERE T.ID < S.ID and S.ColValue IN (T.FirstColumn,T.SecondColumn,T.ThirdColumn,T.FourthColumn, T.FifthColumn) GROUP BY S.ID) S),

Candidates as (select distinct ID,DupID  from DupRecs where cntDups >=4) 
```
Ok, but do we want to delete all records identified in DupID column? Turned out, that it's not even that simple. 

Say, in our sample rows 1 & 2 match. So, do we want to delete the row 2? Well, row 2 matches with the row 5. Which rows of 2/5 we delete and which to keep? That's not easy at all.

So, my final select is

```sql
select Distinct DupID from Candidates where DupID not IN (select ID from Candidates)
```
This select will only produce real duplicates.

Now, this is the whole solution again as one statement:

```sql
;with UnPvt as (select ID, ColValue from tblTEST 
UNPIVOT (ColValue for ColName IN ([FirstColumn],[SecondColumn],[ThirdColumn],[FourthColumn], [FifthColumn])) unpvt),
SameVals as (select * from (select *, COUNT(ID) over (partition by ColValue) as cntSame from UnPvt) X where cntSame >=2),
DupRecs as (select T.*,S.cntDups, S.ID as DupID
 from tblTEST T CROSS APPLY (select S.ID, COUNT(*) as cntDups from SameVals S
WHERE T.ID < S.ID and S.ColValue IN (T.FirstColumn,T.SecondColumn,T.ThirdColumn,T.FourthColumn, T.FifthColumn) GROUP BY S.ID) S),

Candidates as (select distinct ID,DupID  from DupRecs where cntDups >=4) 

select Distinct DupID from Candidates where DupID not IN (select ID from Candidates)

```
Well, even the above solution is not quite right as now it doesn't delete all duplicates.

After some more discussion in the mentioned thread and with the help of Peter Larsson, here is the solution that seems to work for the problem:

```sql
DECLARE  @DupLoop INT 

SET @DupLoop = 1 

WHILE @DupLoop = 1 
  BEGIN 
     
     
    WITH Dups 
         AS (SELECT DISTINCT a.ID AS DuplicateID 
             FROM   dbo.tblTEST AS t 
                    LEFT JOIN dbo.tblTEST AS a 
                      ON a.ID > t.ID 
                         AND a.FirstColumn IN (t.FirstColumn,t.SecondColumn,t.ThirdColumn,t.FourthColumn,
                                               t.FifthColumn) 
                    LEFT JOIN dbo.tblTEST AS b 
                      ON b.ID = a.ID 
                         AND b.SecondColumn IN (t.FirstColumn,t.SecondColumn,t.ThirdColumn,t.FourthColumn,
                                                t.FifthColumn) 
                    LEFT JOIN dbo.tblTEST AS c 
                      ON c.ID = a.ID 
                         AND c.ThirdColumn IN (t.FirstColumn,t.SecondColumn,t.ThirdColumn,t.FourthColumn,
                                               t.FifthColumn) 
                    LEFT JOIN dbo.tblTEST AS d 
                      ON d.ID = a.ID 
                         AND d.FourthColumn IN (t.FirstColumn,t.SecondColumn,t.ThirdColumn,t.FourthColumn,
                                                t.FifthColumn) 
                    LEFT JOIN dbo.tblTEST AS e 
                      ON e.ID = a.ID 
                         AND e.FifthColumn IN (t.FirstColumn,t.SecondColumn,t.ThirdColumn,t.FourthColumn,
                                               t.FifthColumn) 
             WHERE  CASE 
                      WHEN a.ID IS NULL THEN 0 
                      ELSE 1 
                    END + CASE 
                            WHEN b.ID IS NULL THEN 0 
                            ELSE 1 
                          END + CASE 
                                  WHEN c.ID IS NULL THEN 0 
                                  ELSE 1 
                                END + CASE 
                                        WHEN d.ID IS NULL THEN 0 
                                        ELSE 1 
                                      END + CASE 
                                              WHEN e.ID IS NULL THEN 0 
                                              ELSE 1 
                                            END >= 4) 
    DELETE FROM tblTEST 
    WHERE       ID IN (SELECT   TOP ( 1 ) DuplicateID 
                       FROM     Dups 
                       ORDER BY DuplicateID) 
     
    SELECT @DupLoop = @@ROWCOUNT 
  END 

SELECT * 
FROM   tblTest
```
Well, hopefully this was interesting to read as for me trying to solve such problem. Thanks for listening!

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][2] forum or our [Microsoft SQL Server Admin][3] forum**<ins></ins>

 [1]: http://social.msdn.microsoft.com/Forums/en-US/transactsql/thread/8ce895b9-11f1-494c-a63e-e25f08f93bd8
 [2]: http://forum.lessthandot.com/viewforum.php?f=17
 [3]: http://forum.lessthandot.com/viewforum.php?f=22