---
title: Working with Excel to match data 101
author: Ted Krueger (onpnt)
type: post
date: 2009-08-05T16:10:26+00:00
ID: 530
url: /index.php/datamgmt/dbprogramming/working-with-excel-to-match-data-101/
views:
  - 9929
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server

---
As a DBA or DB Developer, it probably seems like a fundamental task to import an excel sheet in order to match some data up to the contents, create a report, import into other tables and such. I was asked yesterday how to do this though by a developer and realize it may not be all that straight forward on the ways to do it. I thought I'd show a few ways just so it's up there.

I like to have a scenario with these things so let's say you just received an email with an Excel attachment and they need additional data on sheet 1 that the application they are using just won't give them. So there are a few quick ways I would do this. 

1) Import the sheet into your work database (DBA in my case), do your work and get out
  
2) A direct insert into the Excel sheet using OPENROWSET, OPENDATASOURCE (this option gets messy on the inserts)

Option 1 is usually how I do it since I can write and validate the query it seems the quickest.

First you need the Excel sheet in the DBA database. Either the import wizard or OPENROWSET again. I use the import wizard for these. It's perfect for it and quick. 

To do that, right click the database, scroll to tasks and select Import Data. Select "Microsoft Excel" in the Data source listing. Note: if you receive an Excel file version 2007+, you need to use Office 12 OLEDB providers. You can read about that [here][1] and how to set the properties or if you allow OPENROWSET then you can simply throw this in there and execute it.

```sql
SELECT * 
INTO EXCELDATA
FROM 
OPENROWSET('Microsoft.ACE.OLEDB.12.0','Excel 12.0;Database=C:ExcelFile.xlsx', 'SELECT * FROM [Sheet1$]')
```
Nice thing about this is, it will work with pre-2007 versions so you don't' have to alter the statement to import other Excel files older as long as you have Office 12 objects installed on the machine importing. If you don't you can do the older statement of

```sql
SELECT * 
INTO EXCELDATA
FROM OPENROWSET('MSDASQL', 
    'Driver={Microsoft Excel Driver (*.xls)};DBQ=C:ExcelFile.xls', 
    'SELECT * FROM [sheet1$]')
```

Back to the import wizard; now that you have the data source, click browse to locate your Excel file. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//xls_1.gif" alt="" title="" width="541" height="519" />
</div>

Hit Next and you should be able to hit next again sense you are working in the database to import directly. If they are not, fill in the blanks as needed for your DB and instance. Leave, "copy data from one or more tables or views" select and hit next in the next screen. In the, "select source tables and views screen, tick the first sheet (or the sheet that has the relevant data) and either leave the name for the destination the same or change it to what you want. I like to change it to a meaningful name and one that is easier to type for my query.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//xls_2.gif" alt="" title="" width="541" height="519" />
</div>

Hit next and then hit finish. That should be it.
  
In the Excel sheet I populated Sheet1 with this from AdventureWorks.

```sql
Select TOP 10000 OrderDate,SalesOrderNumber from Sales.SalesOrderHeader
```

The request at hand is to match up these orders with salesman. Granted this is an odd task and you're going to get much more complexity in real life, but for the example I like to make it a bit easier so we know you'll get it working.
  
So running this

```sql
Select * From BOSSIMPORT
```

We can see our data is in there and waiting for us to complete the task. In AdventureWorks the schema for sales is, Sales. This referes the objects of sales and all pertinent data. Just looking at the design of the schema you can see immediately that Sales.SalesPerson is what you're going to need to fulfill the additional column of sales person to the order numbers. We can then build a query like this 

```sql
Select
	import.*
	,salesper.FullName
	,salesper.SalesPersonID
From
BOSSIMPORT import  
Left Join (
			select 
				names.FirstName + ' ' + names.LastName FullName
				,hdr.SalesPersonID
				,hdr.SalesOrderNumber
			from 
			AdventureWorks.HumanResources.Employee emp
			inner join AdventureWorks.Sales.SalesPerson sls on emp.EmployeeID = sls.SalesPersonID
			inner join AdventureWorks.Person.Contact names on  sls.SalesPersonID = names.ContactID
			Inner Join AdventureWorks.Sales.SalesOrderHeader hdr on sls.SalesPersonID = hdr.SalesPersonID
			) salesper on import.SalesOrderNumber COLLATE DATABASE_DEFAULT 
								= salesper.SalesOrderNumber COLLATE DATABASE_DEFAULT
```

With that then you just do an alter to add the columns on the import table and then an update for the data

```sql
Update import
Set import.FullName = salesper.FullName
	,import.SalesPersonID = salesper.SalesPersonID
From
BOSSIMPORT import  
Left Join (
			select 
				names.FirstName + ' ' + names.LastName FullName
				,hdr.SalesPersonID
				,hdr.SalesOrderNumber
			from 
			AdventureWorks.HumanResources.Employee emp
			inner join AdventureWorks.Sales.SalesPerson sls on emp.EmployeeID = sls.SalesPersonID
			inner join AdventureWorks.Person.Contact names on  sls.SalesPersonID = names.ContactID
			Inner Join AdventureWorks.Sales.SalesOrderHeader hdr on sls.SalesPersonID = hdr.SalesPersonID
			) salesper on import.SalesOrderNumber COLLATE DATABASE_DEFAULT 
								= salesper.SalesOrderNumber COLLATE DATABASE_DEFAULT
```

Now selecting all from the BOSSIMPORT will give you everything. I just copy paste the result set into the Excel file they sent and forward it on back.

The OPENROWSET then can be constructed off the statements above as just

```sql
SELECT * 
INTO BOSSIMPORT
FROM OPENROWSET('MSDASQL', 
    'Driver={Microsoft Excel Driver (*.xls)};DBQ=C:ExcelFile.xls', 
    'SELECT * FROM [sheet1$]')
Go

ALTER TABLE BOSSIMPORT
ADD FullName varchar(101) 
	,SalesPersonID INT
Go

Update import
Set import.FullName = salesper.FullName
	,import.SalesPersonID = salesper.SalesPersonID
From
BOSSIMPORT import  
Left Join (
			select 
				names.FirstName + ' ' + names.LastName FullName
				,hdr.SalesPersonID
				,hdr.SalesOrderNumber
			from 
			AdventureWorks.HumanResources.Employee emp
			inner join AdventureWorks.Sales.SalesPerson sls on emp.EmployeeID = sls.SalesPersonID
			inner join AdventureWorks.Person.Contact names on  sls.SalesPersonID = names.ContactID
			Inner Join AdventureWorks.Sales.SalesOrderHeader hdr on sls.SalesPersonID = hdr.SalesPersonID
			) salesper on import.SalesOrderNumber COLLATE DATABASE_DEFAULT 
								= salesper.SalesOrderNumber COLLATE DATABASE_DEFAULT
Go

select 
	names.FirstName + ' ' + names.LastName FullName
	,hdr.SalesPersonID
	,hdr.SalesOrderNumber
from 
AdventureWorks.HumanResources.Employee emp
inner join AdventureWorks.Sales.SalesPerson sls on emp.EmployeeID = sls.SalesPersonID
inner join AdventureWorks.Person.Contact names on  sls.SalesPersonID = names.ContactID
Inner Join AdventureWorks.Sales.SalesOrderHeader hdr on sls.SalesPersonID = hdr.SalesPersonID
Go
```

So you can get it all done in one execute.

 [1]: http://www.sql-server-performance.com/articles/biz/How_to_Import_Data_From_Excel_2007_p1.aspx