---
title: 'SQL Puzzle:  RIGHT without using the RIGHT function'
author: SQLDenis
type: post
date: 2013-05-22T07:44:00+00:00
excerpt: |
  I haven't done a puzzle for a long time so I figured let's do a simple one. Return the right 6 characters of the column but without using the RIGHT function.
  
  
  Here is what the table looks like
  CREATE TABLE #Puzzle(SomeCol CHAR(7))
  
  INSERT  #Puzzl&hellip;
url: /index.php/datamgmt/dbprogramming/sql-puzzle-right-without-using/
views:
  - 18413
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
tags:
  - puzzle
  - sql server 2000
  - sql server 2005
  - sql server 2008
  - sql server 2012
  - teaser

---
I haven&#8217;t done a puzzle for a long time so I figured let&#8217;s do a simple one. Return the right 6 characters of the column but without using the RIGHT function.

Here is what the table looks like

<pre>CREATE TABLE #Puzzle(SomeCol CHAR(7))

INSERT  #Puzzle VALUES ('AAAAAAA')
INSERT  #Puzzle VALUES (' BBBBBB')
INSERT  #Puzzle VALUES ('CCCCCC')
INSERT  #Puzzle VALUES (' DDDDD ')
INSERT  #Puzzle VALUES (NULL)
INSERT  #Puzzle VALUES ('       ')</pre>

As you can see some rows start with spaces and some end with spaces, one row has the NULL value
  
The output should be a Z followed by the right 6 characters from the column + the length of the column (not the length of the 6 characters that you are returning)

Here is what the output should look like

<pre>ZAAAAAA7
ZBBBBBB7
ZCCCCC 7
ZDDDDD 7
Z0
Z      7</pre>

As you can see spaces should remain and when the value is NULL then there should be a blank. The length for the row with the NULL value should be 0, you cannot hardcode 7 for the length, you have to calculate it.

Post your solution(s) in the comment section, remember no RIGHT function. Let&#8217;s see how many different ways you can come up with to do this