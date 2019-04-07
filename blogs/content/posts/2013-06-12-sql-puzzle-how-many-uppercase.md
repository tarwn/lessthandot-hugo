---
title: SQL Puzzle.. How many uppercase and lowercase characters in a column
author: SQLDenis
type: post
date: 2013-06-12T12:47:00+00:00
ID: 2106
excerpt: |
  In this week's puzzle we are going to try to figure out how many uppercase, how many lowercase characters are in a column, we are also interested in how many are neither uppercase or lowercase
  
  
  Here is the table and the data
  
  CREATE TABLE Puzzle&hellip;
url: /index.php/datamgmt/dbprogramming/sql-puzzle-how-many-uppercase/
views:
  - 26348
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
In this week&#8217;s puzzle we are going to try to figure out how many uppercase, how many lowercase characters are in a column, we are also interested in how many are neither uppercase or lowercase

Here is the table and the data

sql
CREATE TABLE Puzzle
     (Col1 varchar(10));
 
INSERT INTO Puzzle (Col1)
SELECT 'ABCD01234Z'
UNION ALL
SELECT 'AAAAAAAAAA'
UNION ALL
SELECT 'aaaaaaaaaZ'
UNION ALL
SELECT 'a&a&a&a&aA'
UNION ALL
SELECT '1234Tt7890'
```

This is the expected output

<pre>Col1		UpperCase	LowerCase	Neither
ABCD01234Z	5		0		5
AAAAAAAAAA	10		0		0
aaaaaaaaaZ	1		9		0
a&a&a&a&aA	1		5		4
1234Tt7890	1		1		8</pre>

For bonus points, you can add 3 more columns to the output, one column will hold only uppercase characters, one column will hold only lowercase characters, one column will hold only characters that are neither uppercase or lowercase