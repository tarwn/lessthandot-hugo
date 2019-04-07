---
title: Automatically create a SQL Server table in a new database.
author: George Mastros (gmmastros)
type: post
date: 2009-07-03T14:53:14+00:00
ID: 494
url: /index.php/datamgmt/datadesign/automatically-create-a-sql-server-table/
views:
  - 10682
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
I&#8217;ve seen this question numerous times in the forums I like to frequent. The idea is, when you create a new database, you may want that database to have several tables, some of which are automatically populated with data (thinking about relatively static lookup tables here).

The process mentioned here may not be useful for everyone, but it is certainly something to consider.

There is a system database named Model. This database is used when you create a new user database. In fact, any object (table, view, stored procedures, functions, etc…) that exists in the model database will be copied to your newly created database. This may be a blessing and a curse, so use this suggestion wisely. If you have a SQL Instance where you need to set up a new database for each customer, and that is all the instance is used for, then it makes sense to create your objects in the Model database. However, if you have a general purpose instance that you are using for various databases, you probably won’t want to put your objects in the Model database. 

Enough of the chatter, let&#8217;s see how this can be done.

First, create a table in the Model database.

sql
use Model
go

Create Table TestAutoDBCreation(Id Int, Color VarChar(20))
Insert Into TestAutoDBCreation Values(1, 'Red')
Insert Into TestAutoDBCreation Values(2, 'Blue')
```

Now, let’s create a new database.

sql
use Master
go
Create Database NewDatabaseWithModelTable
```
Now, let&#8217;s make sure the table (and it&#8217;s data) exist in the new database.

sql
use NewDatabaseWithModelTable
go
Select * From TestAutoDBCreation
```
As you can see, the table that was created in Model exists in the newly created database. It even has all the data that was loaded in to it. 

Now, let’s clean up after ourselves:

sql
Use Model
go
Drop Table TestAutoDBCreation
go
Use Master
go
Drop Database NewDatabaseWithModelTable
```
