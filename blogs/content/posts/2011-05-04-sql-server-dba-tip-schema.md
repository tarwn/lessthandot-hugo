---
title: SQL Server DBA Tip 7 – Server Security and grouping – Schema Control
author: Ted Krueger (onpnt)
type: post
date: 2011-05-04T10:24:00+00:00
ID: 1134
excerpt: It was a dark and stormy night.  Database User Fred had an idea to venture into tables he wasn't supposed to be in...OK, really that was for my buddy Noel McKinney.  At some point when you are writing, the single largest barrier is the fi&hellip;
url: /index.php/datamgmt/dbadmin/sql-server-dba-tip-schema/
views:
  - 7897
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
It was a dark and stormy night.  Database User Fred had an idea to venture into tables he wasn't supposed to be in...OK, really that was for my buddy Noel McKinney ([Twitter][1] | [Blog][2]).  At some point when you are writing, the single largest barrier is the first line.  Thanks to Noel, I have my first line and we can get to it...

Prior to SQL Server 2005, Schemas were not much more than a form of a database user.  Schemas since the SQL Server 2005 release are now a form of containment in which grouping of objects can be performed.   This is beneficial to security because we can create inner secured groupings of objects in a single database and quickly move objects contained in that group to other areas.  The value in this for a DBA is seen in controlling their own work.  Best practice tells us we should create a user database that we use for maintenance and other administrative tasks on SQL Server.  These databases can quickly become littered with tables and their exact purposes hard to manage.  Using Schemas, DBAs can group the tables into meaningful groups, such as index tables in an IndexMaint schema and DMV collections into a Baseline schema.  Once this is set up, the tables are identified quickly by the schema they are contained in.

**Create a basic Schema**

Create a basic Schema by using the CREATE SCHEMA statement.

```sql
CREATE SCHEMA FinanceTables
GO
```

The FinanceTables Schema will contain all tables that are related to financials for the database ERP.  The database instance has a user login Fred and this login is mapped to a database user in the database ERP.  To restrict Fred to the FinanceTables, the ALTER AUTHORIZATION statement is used.  Once Fred is authorized to alter the schema FinanceTables, Fred can create tables.

```sql
ALTER AUTHORIZATION ON SCHEMA::FinanceTables TO Fred
GO
GRANT CREATE TABLE to Fred
GO
```

Fred is now allowed to create tables in the schema FinanceTables but not allowed to create tables in any other schemas.  To test this, run the following statement to create a table in the schema dbo.

```sql
CREATE TABLE dbo.Sales (MyMula Money)
GO
```

Resulting error message:

<span class="MT_red">Msg 2760, Level 16, State 1, Line 1<br /> The specified schema name "dbo" either does not exist or you do not have permission to use it.</span>

schema to FinanceTables and attempt to run the statement again.

```sql
CREATE TABLE FinanceTables.Sales (MyMula Money)
GO
```

The Sales table is created successfully under the schema FinanceTables, and Fred is in control of it.  The value in this is containing the tables Fred creates and accesses to the entity FinanceTables as well as preventing Fred from creating tables in any other schemas in the database. 

**Moving objects from schema to schema**

DBAs can benefit from schemas by having the ability to quickly move from one schema to another schema.  This is accomplished by using the ALTER SCHEMA statement.

```sql
ALTER SCHEMA IndexMaint TRANSFER dba.indexlog;
GO
```

This change does not truly move the table but redefines the schema that owns it.  This method can be used to change security on a wide range of objects quickly.  Moving the entire contents can be performed as shown in the Wiki entry, "[Move all tables from one schema to another quickly][3]"

Resources

[CREATE SCHEMA][4]

[ALTER SCHEMA][5]

[ALTER AUTHORIZATION][6]

 [1]: http://twitter.com/noelmckinney
 [2]: http://noelmckinney.com/
 [3]: http://wiki.ltd.local/index.php/Transfer_all_tables_to_different_schema
 [4]: http://msdn.microsoft.com/en-us/library/ms189462.aspx
 [5]: http://msdn.microsoft.com/en-us/library/ms173423.aspx
 [6]: http://msdn.microsoft.com/en-us/library/ms187359.aspx