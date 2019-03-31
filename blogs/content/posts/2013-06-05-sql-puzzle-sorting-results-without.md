---
title: SQL Puzzle.. Sorting results without using ORDER BY
author: SQLDenis
type: post
date: 2013-06-05T19:25:00+00:00
excerpt: |
  Time for this week's puzzle/teaser. I want to return the results in ascending order but without using ORDER BY
  
  If you run this code
  
  CREATE TABLE Puzzle
       (Col1 varchar(20) NOT NULL PRIMARY KEY CLUSTERED,
        Col2 varchar(20) NOT NULL UNIQUE&hellip;
url: /index.php/datamgmt/dbprogramming/mssqlserver/sql-puzzle-sorting-results-without/
views:
  - 32799
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server
tags:
  - puzzle
  - sql server 2000
  - sql server 2005
  - sql server 2008
  - sql server 2012
  - teaser

---
Time for this week&#8217;s puzzle/teaser. I want to return the results in ascending order but without using ORDER BY

If you run this code

<pre>CREATE TABLE Puzzle
     (Col1 varchar(20) NOT NULL PRIMARY KEY CLUSTERED,
      Col2 varchar(20) NOT NULL UNIQUE NONCLUSTERED);

INSERT INTO Puzzle (Col1, Col2)
SELECT 'Z', 'AA'
UNION ALL
SELECT 'A', 'BB'
UNION ALL
SELECT 'B', 'CC'
UNION ALL
SELECT 'C', 'DD'
UNION ALL
SELECT 'M', 'EE';

SELECT Col1 FROM Puzzle;
DROP TABLE Puzzle;</pre>

you get these results

Col1
  
Z
  
A
  
B
  
C
  
M

What I want to see is the following

Col1
  
A
  
B
  
C
  
M
  
Z

Without using ORDER BY, how would you make the SELECT query return Col1 in ascending order? You can&#8217;t make changes to the table, all that you are allowed to modify is this part

<pre>SELECT Col1 FROM Puzzle;</pre>

**Warning**
  
Do not use these suggestion in production code, the only real way to guarantee order of a result set is by using ORDER BY