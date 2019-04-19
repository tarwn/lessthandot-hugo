---
title: Column name or number of supplied values does not match table definition when dealing with temp tables
author: SQLDenis
type: post
date: 2013-03-03T19:25:00+00:00
ID: 2020
excerpt: |
  The other day I was doing some testing and then from the same connection I executed a stored procedure only to be greeted with the following message
  
  Msg 213, Level 16, State 1, Procedure prTestTemp, Line 5
  Column name or number of supplied values do&hellip;
url: /index.php/datamgmt/dbprogramming/column-name-or-number-of/
views:
  - 24083
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - gotcha
  - sql server 2000
  - sql server 2005
  - sql server 2008
  - sql server 2012
  - tables
  - temp tables

---
The other day I was doing some testing and then from the same connection I executed a stored procedure only to be greeted with the following message

Msg 213, Level 16, State 1, Procedure prTestTemp, Line 5
  
Column name or number of supplied values does not match table definition.

I looked at the proc, hasn't changed in months, I decided to run it from a different window and no problem. I took me a couple of minutes to realize what was going on.

Let's duplicate this here with some code that you can run. Make sure that you run the code all in the same window

First create this stored procedure, do not close this window after creation

```sql
CREATE PROCEDURE prTestTemp
AS

CREATE TABLE #temp (id int)
INSERT #temp VALUES(1)

SELECT * FROM #temp
GO
```

In the same window now create the following temp table

```sql
CREATE TABLE #temp (id int, id2 int)
INSERT #temp VALUES(1,2)

SELECT * FROM #temp
```

Now run the procedure

```sql
EXEC prTestTemp
```

Here is the error
  
_Msg 213, Level 16, State 1, Procedure prTestTemp, Line 5
  
Column name or number of supplied values does not match table definition._

Drop the table and we will try again

```sql
DROP TABLE #temp
```

Run the procedure again

```sql
EXEC prTestTemp
```

This time there was no error

Let's do another experiment, create the table again

```sql
CREATE TABLE #temp (id int, id2 int)
INSERT #temp VALUES(1,2)

SELECT * FROM #temp
```

Now, let's try modifying the procedure, change create to alter and run it again

```sql
ALTER PROCEDURE prTestTemp
AS

CREATE TABLE #temp (id int)
INSERT #temp VALUES(1)

SELECT * FROM #temp
GO
```

Here is the error
  
_Msg 213, Level 16, State 1, Procedure prTestTemp, Line 5
  
Column name or number of supplied values does not match table definition._

As you can see, you can't modify the procedure in the same window, copy and paste the code in another window and you won't have a problem.

The reason you run into this because the temporary table is local to your connection, it is not dropped until you close the connection. If you have a temporary table with the same name inside a proc that you try to execute you will run into this problem. One way to avoid this is by not naming a temporary table the same in every stored procedure that you have, for example #temp

BTW, doing something like this is no problem

```sql
CREATE PROCEDURE prTestTemp2
AS
 
CREATE TABLE #temp (id int, id2 int)
INSERT #temp VALUES(1,2)
 
SELECT * FROM #temp

EXEC prTestTemp
GO
```

As you can see both procedure have a temporary table named #temp and you get back two resultsets, one has 1 column, the other one has 2 columns

Just be aware of how this works because you could be scratching your head for hours trying to figure something like this out