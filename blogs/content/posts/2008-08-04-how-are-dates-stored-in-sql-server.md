---
title: How Are Dates Stored In SQL Server?
author: SQLDenis
type: post
date: 2008-08-04T13:57:08+00:00
ID: 100
url: /index.php/datamgmt/datadesign/how-are-dates-stored-in-sql-server/
views:
  - 27088
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
Internally dates are stored as 2 integers. The first integer is the number of dates before or after the base date (1900/01/01). The second integer stores the number of clock ticks after midnight, each tick is 1/300 of a second. 

So if we run the following code for the base date (1900/01/01) 

```sql
DECLARE @d DATETIME 
SELECT @d = '1900-01-01 00:00:00.000' 


SELECT CONVERT(INT,SUBSTRING(CONVERT(VARBINARY(8),@d),1,4)) AS DateInt, 
SUBSTRING(CONVERT(VARBINARY(8),@d),1,4) AS DateBinary 
SELECT CONVERT(INT,SUBSTRING(CONVERT(VARBINARY(8),@d),5,4)) AS TimeInt, SUBSTRING(CONVERT(VARBINARY(8),@d),5,4) AS TimeBinary 
Go 
```

The results are 

DateInt DateBinary
  
———– ———-
  
0 0x00000000 

TimeInt TimeBinary
  
———– ———-
  
0 0x00000000 

If we use the max date 9999/12/31 

```sql
DECLARE @d DATETIME 
SELECT @d = '9999-12-31 23:59:59.997' 


SELECT CONVERT(INT,SUBSTRING(CONVERT(VARBINARY(8),@d),1,4)) AS DateInt, 
SUBSTRING(CONVERT(VARBINARY(8),@d),1,4) AS DateBinary 
SELECT CONVERT(INT,SUBSTRING(CONVERT(VARBINARY(8),@d),5,4)) AS TimeInt, SUBSTRING(CONVERT(VARBINARY(8),@d),5,4) AS TimeBinary 
Go 
```

we get the following result 

DateInt DateBinary
  
———– ———-
  
2958463 0x002D247F 

TimeInt TimeBinary
  
———– ———-
  
25919999 0x018B81FF 

If you take binary values and convert to datetime you get the following results 

```sql
SELECT CONVERT(DATETIME,0x0000000000000001) --1 Tick 1/300 of a second 
```

——————————————————
  
–1900-01-01 00:00:00.003 

```sql
SELECT CONVERT(DATETIME,0x000000000000012C) -- 1 minute = 300 ticks 
```

——————————————————
  
–1900-01-01 00:00:01.000 

```sql
SELECT CONVERT(INT,0x12C) --= 300 
SELECT CONVERT(VARBINARY(3),300) --= 0x00012C 

SELECT CONVERT(DATETIME,0x0000000100000000) --add 1 day 
```

——————————————————
  
–1900-01-02 00:00:00.000 

For smalldatetime the time is stored as the number of minutes after midnight 

Now here is some fun stuff 

```sql
DECLARE @d DATETIME 
SELECT @d = .0 
SELECT @d 
GO 
```

——————————————————
  
–1900-01-01 00:00:00.000 

```sql
DECLARE @d DATETIME 
SELECT @d = .1 
SELECT @d 
GO 
```

——————————————————
  
–1900-01-01 02:24:00.000 

```sql
DECLARE @d DATETIME 
SELECT @d = .12 
SELECT @d 
GO 
```

——————————————————
  
–1900-01-01 02:52:48.000 

```sql
DECLARE @d DATETIME 
SELECT @d = '0' 
SELECT @d 
GO 
```

Server: Msg 241, Level 16, State 1, Line 2
  
Syntax error converting datetime from character string. 

```sql
DECLARE @d DATETIME 
SELECT @d = 0 
SELECT @d 
GO 
```

——————————————————
  
–1900-01-01 00:00:00.000 

So there is no implicit conversion, o is fine 'o' is not 

```sql
DECLARE @d DATETIME 
SELECT @d = 20061030 
SELECT @d 
GO 
```

Server: Msg 8115, Level 16, State 2, Line 2
  
Arithmetic overflow error converting expression to data type datetime. 

```sql
DECLARE @d DATETIME 
SELECT @d = '20061030' 
SELECT @d 
GO 
```

——————————————————
  
–2006-10-30 00:00:00.000 

Here we have the reverse, the varchar value is fine but the int is not.
  
This happens because the max integer value that a datetime can take is 36523
  
If we run the following we are okay 

```sql
DECLARE @d DATETIME 
SELECT @d = 2958463 
SELECT @d 
GO 
```

——————————————————
  
–9999-12-31 00:00:00.000