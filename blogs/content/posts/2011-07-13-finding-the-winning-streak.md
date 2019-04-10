---
title: Finding the Winning Streak
author: Naomi Nosonovsky
type: post
date: 2011-07-14T00:32:00+00:00
ID: 1253
excerpt: |
  Ok, I hope I got your attention with this title :)
  
  I want to share my solution of an interesting problem I saw today at tek-tips forum SQL 2005: Find the longest sequence of events within a table
url: /index.php/datamgmt/datadesign/finding-the-winning-streak/
views:
  - 17903
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
Ok, I hope I got your attention with this title 🙂

I want to share my solution of an interesting problem I saw today at tek-tips forum [SQL 2005: Find longest sequence of events within a table][1]

Given the following table:

<div class="tables">
  <table border="1" cellpadding ="5" cellspacing = "5"> <caption style="color:blue;font-weight:bold">Team Table</caption> 
  
  <tr>
    <th align="left">
      Team
    </th>
    
    <th align="left">
      Date
    </th>
    
    <th align="left">
      WiLose
    </th>
  </tr>
  
  <tr>
    <td align="left">
      A
    </td>
    
    <td align="left">
      2010-01-01 00:00:00.000
    </td>
    
    <td align="left">
      Win
    </td>
  </tr>
  
  <tr>
    <td align="left">
      A
    </td>
    
    <td align="left">
      2010-01-07 00:00:00.000
    </td>
    
    <td align="left">
      Lose
    </td>
  </tr>
  
  <tr>
    <td align="left">
      A
    </td>
    
    <td align="left">
      2010-01-13 00:00:00.000
    </td>
    
    <td align="left">
      Lose
    </td>
  </tr>
  
  <tr>
    <td align="left">
      A
    </td>
    
    <td align="left">
      2010-01-19 00:00:00.000
    </td>
    
    <td align="left">
      Lose
    </td>
  </tr>
  
  <tr>
    <td align="left">
      A
    </td>
    
    <td align="left">
      2010-01-25 00:00:00.000
    </td>
    
    <td align="left">
      Lose
    </td>
  </tr>
  
  <tr>
    <td align="left">
      A
    </td>
    
    <td align="left">
      2010-01-31 00:00:00.000
    </td>
    
    <td align="left">
      Lose
    </td>
  </tr>
  
  <tr>
    <td align="left">
      A
    </td>
    
    <td align="left">
      2010-02-06 00:00:00.000
    </td>
    
    <td align="left">
      Win
    </td>
  </tr>
  
  <tr>
    <td align="left">
      A
    </td>
    
    <td align="left">
      2010-02-12 00:00:00.000
    </td>
    
    <td align="left">
      Lose
    </td>
  </tr>
  
  <tr>
    <td align="left">
      A
    </td>
    
    <td align="left">
      2010-02-18 00:00:00.000
    </td>
    
    <td align="left">
      Win
    </td>
  </tr>
  
  <tr>
    <td align="left">
      B
    </td>
    
    <td align="left">
      2010-01-01 00:00:00.000
    </td>
    
    <td align="left">
      Lose
    </td>
  </tr>
  
  <tr>
    <td align="left">
      B
    </td>
    
    <td align="left">
      2010-01-07 00:00:00.000
    </td>
    
    <td align="left">
      Win
    </td>
  </tr>
  
  <tr>
    <td align="left">
      B
    </td>
    
    <td align="left">
      2010-01-13 00:00:00.000
    </td>
    
    <td align="left">
      Lose
    </td>
  </tr>
  
  <tr>
    <td align="left">
      B
    </td>
    
    <td align="left">
      2010-01-19 00:00:00.000
    </td>
    
    <td align="left">
      Win
    </td>
  </tr>
  
  <tr>
    <td align="left">
      B
    </td>
    
    <td align="left">
      2010-01-25 00:00:00.000
    </td>
    
    <td align="left">
      Win
    </td>
  </tr>
  
  <tr>
    <td align="left">
      B
    </td>
    
    <td align="left">
      2010-01-31 00:00:00.000
    </td>
    
    <td align="left">
      Win
    </td>
  </tr>
  
  <tr>
    <td align="left">
      B
    </td>
    
    <td align="left">
      2010-02-06 00:00:00.000
    </td>
    
    <td align="left">
      Win
    </td>
  </tr>
  
  <tr>
    <td align="left">
      B
    </td>
    
    <td align="left">
      2010-02-12 00:00:00.000
    </td>
    
    <td align="left">
      Lose
    </td>
  </tr>
  
  <tr>
    <td align="left">
      B
    </td>
    
    <td align="left">
      2010-02-18 00:00:00.000
    </td>
    
    <td align="left">
      Lose
    </td>
  </tr>
  
  <tr>
    <td align="left">
      C
    </td>
    
    <td align="left">
      2010-01-01 00:00:00.000
    </td>
    
    <td align="left">
      Lose
    </td>
  </tr>
  
  <tr>
    <td align="left">
      C
    </td>
    
    <td align="left">
      2010-01-07 00:00:00.000
    </td>
    
    <td align="left">
      Lose
    </td>
  </tr>
  
  <tr>
    <td align="left">
      C
    </td>
    
    <td align="left">
      2010-01-13 00:00:00.000
    </td>
    
    <td align="left">
      Lose
    </td>
  </tr>
  
  <tr>
    <td align="left">
      C
    </td>
    
    <td align="left">
      2010-01-19 00:00:00.000
    </td>
    
    <td align="left">
      Lose
    </td>
  </tr>
  
  <tr>
    <td align="left">
      C
    </td>
    
    <td align="left">
      2010-01-25 00:00:00.000
    </td>
    
    <td align="left">
      Lose
    </td>
  </tr>
  
  <tr>
    <td align="left">
      C
    </td>
    
    <td align="left">
      2010-01-31 00:00:00.000
    </td>
    
    <td align="left">
      Win
    </td>
  </tr>
  
  <tr>
    <td align="left">
      C
    </td>
    
    <td align="left">
      2010-02-06 00:00:00.000
    </td>
    
    <td align="left">
      Win
    </td>
  </tr>
  
  <tr>
    <td align="left">
      C
    </td>
    
    <td align="left">
      2010-02-12 00:00:00.000
    </td>
    
    <td align="left">
      Lose
    </td>
  </tr>
  
  <tr>
    <td align="left">
      C
    </td>
    
    <td align="left">
      2010-02-18 00:00:00.000
    </td>
    
    <td align="left">
      Win
    </td>
  </tr></table>
</div>

find the biggest number of consecutive wins or loses regardless of the team.

Here is the code that creates our team table:

sql
CREATE TABLE team ( 
  Team   CHAR(1), 
  DATE   DATETIME, 
  WiLose VARCHAR(10)) 

SET DATEFORMAT  dmy 

INSERT INTO team 
VALUES     ('A','01/01/2010','Win') 

INSERT INTO team 
VALUES     ('A','07/01/2010','Lose') 

INSERT INTO team 
VALUES     ('A','13/01/2010','Lose') 

INSERT INTO team 
VALUES     ('A','19/01/2010','Lose') 

INSERT INTO team 
VALUES     ('A','25/01/2010','Lose') 

INSERT INTO team 
VALUES     ('A','31/01/2010','Lose') 

INSERT INTO team 
VALUES     ('A','06/02/2010','Win') 

INSERT INTO team 
VALUES     ('A','12/02/2010','Lose') 

INSERT INTO team 
VALUES     ('A','18/02/2010','Win') 

INSERT INTO team 
VALUES     ('B','01/01/2010','Lose') 

INSERT INTO team 
VALUES     ('B','07/01/2010','Win') 

INSERT INTO team 
VALUES     ('B','13/01/2010','Lose') 

INSERT INTO team 
VALUES     ('B','19/01/2010','Win') 

INSERT INTO team 
VALUES     ('B','25/01/2010','Win') 

INSERT INTO team 
VALUES     ('B','31/01/2010','Win') 

INSERT INTO team 
VALUES     ('B','06/02/2010','Win') 

INSERT INTO team 
VALUES     ('B','12/02/2010','Lose') 

INSERT INTO team 
VALUES     ('B','18/02/2010','Lose') 

INSERT INTO team 
VALUES     ('C','01/01/2010','Lose') 

INSERT INTO team 
VALUES     ('C','07/01/2010','Lose') 

INSERT INTO team 
VALUES     ('C','13/01/2010','Lose') 

INSERT INTO team 
VALUES     ('C','19/01/2010','Lose') 

INSERT INTO team 
VALUES     ('C','25/01/2010','Lose') 

INSERT INTO team 
VALUES     ('C','31/01/2010','Win') 

INSERT INTO team 
VALUES     ('C','06/02/2010','Win') 

INSERT INTO team 
VALUES     ('C','12/02/2010','Lose') 

INSERT INTO team 
VALUES     ('C','18/02/2010','Win') 

SET DATEFORMAT  mdy 

SELECT * 
FROM   team
```
This problem is known as 'finding islands of data', and I always refer to this blog by Plamen Ratchev [Refactoring Ranges][2] when I am facing such scenario.

The idea of a solution is to first identify the blocks of consecutive wins or loses by each team. Applying the idea from Plamen's blog first step will be

sql
;WITH cte AS (SELECT *, 
ROW_NUMBER() OVER 
(
PARTITION BY team ORDER BY DATE
) - ROW_NUMBER() OVER (PARTITION BY team, wilose ORDER BY DATE) AS [GroupID]
FROM team),
```
By assigning GroupID we identified blocks of winning or losing streaks.

The last two steps are simple enough:

sql
-- Calculate Wins/Loses totals per each team/block
cte1 AS (SELECT team, 
SUM(CASE WHEN WiLose = 'Win' THEN 1 ELSE 0 END) AS [Wins],
SUM(CASE WHEN WiLose = 'Lose' THEN 1 ELSE 0 END) AS [Loses]
FROM cte
GROUP BY Team, GroupID),

-- Rank from highest to lowest wins / loses
cteSummary AS (SELECT *, RANK() OVER (ORDER BY Wins DESC) AS WinsRank,
RANK() OVER (ORDER BY Loses DESC) AS LosesRank
FROM cte1)
-- Get final results
SELECT Team, [Wins],[Loses]
 FROM cteSummary WHERE WinsRank = 1 OR LosesRank = 1
ORDER BY Team
```
So, correctly identifying a known pattern helps to solve such problems very quickly.

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][3] forum or our [Microsoft SQL Server Admin][4] forum**<ins></ins>

 [1]: http://tek-tips.com/viewthread.cfm?qid=1654675&page=1
 [2]: http://pratchev.blogspot.com/2010/02/refactoring-ranges.html
 [3]: http://forum.ltd.local/viewforum.php?f=17
 [4]: http://forum.ltd.local/viewforum.php?f=22