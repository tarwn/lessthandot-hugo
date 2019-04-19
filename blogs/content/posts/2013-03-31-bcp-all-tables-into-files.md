---
title: BCP all tables into files from a database
author: SQLDenis
type: post
date: 2013-03-31T11:42:00+00:00
ID: 2057
excerpt: |
  Sometimes you want to dump the data from all the tables in a database into files. There is really no fast and easy way to do this. Fortunately it is very easy to roll your own solution. Let's look at what we need to do
  
  1) we need to grab all the tabl&hellip;
url: /index.php/datamgmt/dbprogramming/bcp-all-tables-into-files/
views:
  - 46934
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - bcp
  - bulk export
  - bulk import
  - data migration

---
Sometimes you want to dump the data from all the tables in a database into files. There is really no fast and easy way to do this. Fortunately it is very easy to roll your own solution. Let's look at what we need to do

1) we need to grab all the tables in the database
  
2) we need to make sure that the table names are valid
  
3) we need to specify the output directory
  
4) we need to make sure that the file names are valid
  
5) we need to specify how we are connecting to SQL Server

**We need to grab all the tables in the database**
  
The query to grab all the names is

```sql
SELECT name FROM sys.tables
```

However if you use schemas then that won't work, you need to do the following

```sql
SELECT SCHEMA_NAME(SCHEMA_ID),name 
FROM sys.tables
```

The reason for this is that you can have the same table name in different schemas, you would only get the one in the default schema

**We need to make sure that the table names are valid**
  
Sometimes you have table names that have spaces in them or start perhaps with a number. If you have tables like that, you have to put brackets around it. One way to put brackets around tables names is to just do something like this `'[' + name + ']'` another way is to use the QUOTENAME function

```sql
SELECT   QUOTENAME(SCHEMA_NAME(SCHEMA_ID))+ '.'
+  QUOTENAME(name) FROM sys.tables
```

**We need to specify the output directory**
  
You need to tell bcp where to dump the files

**We need to make sure that the file names are valid**
  
Windows does not allow for certain characters in file names

< (less than) > (greater than)
  
: (colon)
  
” (double quote)
  
/ (forward slash)
   
(backslash)
  
| (vertical bar or pipe)
  
? (question mark)
  
* (asterisk)
  
If you have to give the files to be imported on a Linux/unix systems then you want to eliminate spaces as well
  
In that case you end up with something like this

```sql
SELECT    REPLACE(SCHEMA_NAME(schema_id),' ','') + '_' 
+  REPLACE(name,' ','') 
+  QUOTENAME(name) FROM sys.tables
```

**We need to specify how we are connecting to SQL Server**
  
You can use password and username to connect to SQL Server or you can use a trusted connections

## Putting it all together

Here is the complete query

```sql
SELECT 'EXEC xp_cmdshell ''bcp '           --bcp
+  QUOTENAME(DB_NAME())+ '.'               --database name
+  QUOTENAME(SCHEMA_NAME(SCHEMA_ID))+ '.'  -- schema
+  QUOTENAME(name)						   -- table
+ ' out c:temp'                          -- output directory
+  REPLACE(SCHEMA_NAME(schema_id),' ','') + '_' 
+  REPLACE(name,' ','')                    -- file name
+ '.txt -T -c'''   -- extension, security 
FROM sys.tables
```

Running that query will give you something like the following

```sql
EXEC xp_cmdshell 'bcp [AdventureWorks2012].[Production].[ScrapReason] out c:tempProduction_ScrapReason.txt -T -c'

EXEC xp_cmdshell 'bcp [AdventureWorks2012].[HumanResources].[Shift] out c:tempHumanResources_Shift.txt -T -c'

EXEC xp_cmdshell 'bcp [AdventureWorks2012].[Production].[ProductCategory] out c:tempProduction_ProductCategory.txt -T -c'

EXEC xp_cmdshell 'bcp [AdventureWorks2012].[Purchasing].[ShipMethod] out c:tempPurchasing_ShipMethod.txt -T -c'

EXEC xp_cmdshell 'bcp [AdventureWorks2012].[Production].[ProductCostHistory] out c:tempProduction_ProductCostHistory.txt -T -c'
```

You can now take that and run it. When you run it, you will see the following output

NULL
  
Starting copy...
  
NULL
  
32 rows copied.
  
Network packet size (bytes): 4096
  
Clock Time (ms.) Total : 1 Average : (32000.00 rows per sec.)
  
NULL

Sometines you don't want to see that output,In that case we need to [suppress xp_cmdshell output][1], you do this by adding ,no_output at the end

```sql
SELECT 'EXEC xp_cmdshell ''bcp '           --bcp
+  QUOTENAME(DB_NAME())+ '.'               --database name
+  QUOTENAME(SCHEMA_NAME(SCHEMA_ID))+ '.'  -- schema
+  QUOTENAME(name)						   -- table
+ ' out c:temp'                          -- output directory
+  REPLACE(SCHEMA_NAME(schema_id),' ','') + '_' 
+  REPLACE(name,' ','')                    -- file name
+ '.txt -T -c'',no_output'   -- extension, security, no output 
FROM sys.tables
```

Now you get something like the following

```sql
EXEC xp_cmdshell 'bcp [AdventureWorks2012].[Production].[ScrapReason] out c:tempProduction_ScrapReason.txt -T -c',no_output

EXEC xp_cmdshell 'bcp [AdventureWorks2012].[HumanResources].[Shift] out c:tempHumanResources_Shift.txt -T -c',no_output

EXEC xp_cmdshell 'bcp [AdventureWorks2012].[Production].[ProductCategory] out c:tempProduction_ProductCategory.txt -T -c',no_output

EXEC xp_cmdshell 'bcp [AdventureWorks2012].[Purchasing].[ShipMethod] out c:tempPurchasing_ShipMethod.txt -T -c',no_output
```

There you have it, a quick and dirty version to dump all the tables into files.

You can of course enhance this by creating a proc where you can specify only a certain schema, delimiters, how to connect etc etc

If you don't want to use xp\_cmdshell, you can also dump the results without the xp\_cmdshell part into a BAT file and call it from DOS or PowerShell

That query would look like this

```sql
SELECT 'bcp '           --bcp
+  QUOTENAME(DB_NAME())+ '.'               --database name
+  QUOTENAME(SCHEMA_NAME(SCHEMA_ID))+ '.'  -- schema
+  QUOTENAME(name)						   -- table
+ ' out c:temp'                          -- output directory
+  REPLACE(SCHEMA_NAME(schema_id),' ','') + '_' 
+  REPLACE(name,' ','')                    -- file name
+ '.txt -T -c'   -- extension, security, 
FROM sys.tables
```


 [1]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/supressing-xp_cmdshell-output