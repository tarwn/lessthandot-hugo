---
title: How much longer will the SQL Server database restore take
author: SQLDenis
type: post
date: 2011-09-02T11:12:00+00:00
ID: 1306
excerpt: |
  Frequently you will be asked how much longer a restore will take because someone needs to do something with that specific database that is restoring right now
  
  Of course we all know that the RESTORE DATABASE command has the STATS n option, this will g&hellip;
url: /index.php/datamgmt/datadesign/how-much-longer-will-the/
views:
  - 21326
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - dmv
  - dynamic management views
  - restore

---
Frequently you will be asked how much longer a restore will take because someone needs to do something with that specific database that is restoring right now

Of course we all know that the RESTORE DATABASE command has the STATS n option, this will give you the percentage completed. This is nice but it doesn't tell you when it will complete and if someone else started the restore how will you know how long it will take in that case?

Fear not, here is a query that will tell you exactly how long

```sql
SELECT	
    d.PERCENT_COMPLETE AS [%Complete],
    d.TOTAL_ELAPSED_TIME/60000 AS ElapsedTimeMin,
    d.ESTIMATED_COMPLETION_TIME/60000	AS TimeRemainingMin,
    d.TOTAL_ELAPSED_TIME*0.00000024 AS ElapsedTimeHours,
    d.ESTIMATED_COMPLETION_TIME*0.00000024	AS TimeRemainingHours,
    s.text AS Command
FROM	sys.dm_exec_requests d 
CROSS APPLY sys.dm_exec_sql_text(d.sql_handle)as s
WHERE  d.COMMAND LIKE 'RESTORE DATABASE%'
ORDER	BY 2 desc, 3 DESC
```

Here is the output for a fairly large database restore that I started last night

<div class="tables">
  <table>
    <tr>
      <th>
        %Complete
      </th>
      
      <th>
        ElapsedTimeMin
      </th>
      
      <th>
        TimeRemainingMin
      </th>
      
      <th>
        ElapsedTimeHours
      </th>
      
      <th>
        TimeRemainingHours
      </th>
      
      <th>
        Command
      </th>
    </tr>
    
    <tr>
      <td>
        78.6186
      </td>
      
      <td>
        766
      </td>
      
      <td>
        208
      </td>
      
      <td>
        11.03301576
      </td>
      
      <td>
        2.99693760
      </td>
      
      <td>
        RESTORE database SomeDB
      </td>
    </tr>
    
    <table>
      <p>
        As you can see the query returns elapsed time both in hours and minutes, time remaining both in hours and minutes and also the percentage complete
      </p>
      
      <p>
        Hopefully this will help you with those nagging types.......<br /> </table> </table> </div>