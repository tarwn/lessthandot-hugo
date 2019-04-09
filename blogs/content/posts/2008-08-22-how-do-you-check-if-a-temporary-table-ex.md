---
title: How Do You Check If A Temporary Table Exists In SQL Server
author: SQLDenis
type: post
date: 2008-08-22T12:57:47+00:00
ID: 112
url: /index.php/datamgmt/datadesign/how-do-you-check-if-a-temporary-table-ex/
views:
  - 29906
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - howto
  - programming
  - sql server
  - t-sql
  - tip
  - trick

---
I see more and more people asking how to check if a temporary table exists. How do you check if a temp table exists? 

You can use IF OBJECT_ID(&#8216;tempdb..#temp') IS NOT NULL Let's see how it works 

sql
--Create table 
USE Norhtwind 
GO 

CREATE TABLE #temp(id INT) 

--Check if it exists 
IF OBJECT_ID('tempdb..#temp') IS NOT NULL 
BEGIN 
PRINT '#temp exists!' 
END 
ELSE 
BEGIN 
PRINT '#temp does not exist!' 
END 

--Another way to check with an undocumented optional second parameter 
IF OBJECT_ID('tempdb..#temp','u') IS NOT NULL 
BEGIN 
PRINT '#temp exists!' 
END 
ELSE 
BEGIN 
PRINT '#temp does not exist!' 
END 



--Don't do this because this checks the local DB and will return does not exist 
IF OBJECT_ID('tempdb..#temp','local') IS NOT NULL 
BEGIN 
PRINT '#temp exists!' 
END 
ELSE 
BEGIN 
PRINT '#temp does not exist!' 
END 


--unless you do something like this 
USE tempdb 
GO 

--Now it exists again 
IF OBJECT_ID('tempdb..#temp','local') IS NOT NULL 
BEGIN 
PRINT '#temp exists!' 
END 
ELSE 
BEGIN 
PRINT '#temp does not exist!' 
END 

--let's go back to Norhtwind again 
USE Norhtwind 
GO 


--Check if it exists 
IF OBJECT_ID('tempdb..#temp') IS NOT NULL 
BEGIN 
PRINT '#temp exists!' 
END 
ELSE 
BEGIN 
PRINT '#temp does not exist!' 
END 
```

now open a new window from Query Analyzer (CTRL + N) and run this code again 

sql
--Check if it exists 
IF OBJECT_ID('tempdb..#temp') IS NOT NULL 
BEGIN 
PRINT '#temp exists!' 
END 
ELSE 
BEGIN 
PRINT '#temp does not exist!' 
END 
```

It doesn't exist and that is correct since it's a local temp table not a global temp table 

Well let's test that statement 

sql
--create a global temp table 
CREATE TABLE ##temp(id INT) --Notice the 2 pound signs, that's how you create a global variable 

--Check if it exists 
IF OBJECT_ID('tempdb..##temp') IS NOT NULL 
BEGIN 
PRINT '##temp exists!' 
END 
ELSE 
BEGIN 
PRINT '##temp does not exist!' 
END 
```

It exists, right?
  
Now run the same code in a new Query Analyzer window (CTRL + N) 

sql
--Check if it exists 
IF OBJECT_ID('tempdb..##temp') IS NOT NULL 
BEGIN 
PRINT '##temp exists!' 
END 
ELSE 
BEGIN 
PRINT '##temp does not exist!' 
END 
```

And yes this time it does exist since it's a global table

I have also added this to our wiki, read it here: [Check If Temporary Table Exists][1]

 [1]: http://wiki.ltd.local/index.php/Check_If_Temporary_Table_Exists