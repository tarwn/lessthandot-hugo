---
title: SQL Server DBA Tip 3 – Server Configuration – Model Database
author: Ted Krueger (onpnt)
type: post
date: 2011-04-28T10:18:00+00:00
ID: 1133
excerpt: 'The Model database is used as a template for all user databases that are created in SQL Server.  This means the options, tables, routines and many other objects and settings that are in Model, will be in new user databases.  Knowing that Model is utiliz&hellip;'
url: /index.php/datamgmt/dbadmin/sql-server-dba-tip-6/
views:
  - 5221
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
The Model database is used as a template for all user databases that are created in SQL Server.  This means the options, tables, routines and many other objects and settings that are in Model, will be in new user databases.  Knowing that Model is utilized for this task can be useful by taking advantage of it by changing Model to match the best practices that fit your SQL Servers.  To see a full list of database options that can be changed and flow to new user databases, read [Model Database][1].

**Configuring Model for Recovery**

A quick view into the value of altering Model to manage new user database creations can be found in pre-setting the recovery model.  On development and some other SQL Server installations that may not require a full recovery model, databases are often changed after they are created to be set in the simple recovery model.  Instead of adding that extra step to new databases or preventing the step from being forgotten all together, change Model to use a simple recovery model.

```sql
ALTER DATABASE MODEL SET RECOVERY SIMPLE
```


Now create a new database with all defaults set

```sql
CREATE DATABASE DeleteThisTest
```


 

To check the recovery model of the new database, DeleteThisTest, run the following query on the sys.databases view.

```sql
select recovery_model_desc from sys.databases where name = 'DeleteThisTest'
```


 

The results from the above query show a recovery\_model\_desc of SIMPLE.

 

**Tables, Procedures and Functions**

DBAs often create certain tables in every database.  This may be a table such as a numbers table or a common procedure across all databases.  To ensure those objects are in all new databases, adding them to the Model will also include them in the new user database.  This should be used with much restraint and limit objects that are always created in all databases.  Creating a hundred tables in every database is not a good idea. 

**Security**

Although it is possible to alter the model databases security, it can be misused poorly.  This option is being noted in this post because at times a DBA may find users in new databases that cannot be explained.  These users may have been created from another DBA adding them to the model database.  Test this scenario by adding a user to Model and then creating a new database.  The user that was created in Model will be in the new database that was created. 

**Conclusion**

Use the Model Database to your advantage for setting defaults on new user databases.  Some of the defaults that can cause problems when gone uncontrolled are easily covered by changing them in Model so they are then changed in new databases.

 [1]: http://msdn.microsoft.com/en-us/library/ms186388.aspx