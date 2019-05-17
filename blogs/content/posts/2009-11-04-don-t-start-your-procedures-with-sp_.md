---
title: Don't start your procedures with SP_
author: George Mastros (gmmastros)
type: post
date: 2009-11-04T13:16:28+00:00
ID: 609
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/don-t-start-your-procedures-with-sp_/
aliases:
  - /index.php/datamgmt/dbprogramming/mssqlserver/don-t-start-your-procedures-with-sp_/
views:
  - 69840
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
When SQL Server executes a stored procedure, it first checks to see if it is a built-in stored procedure (system supplied). It checks the master database for the existence of this procedure. If the procedure is not found, it will search the user database. It doesn't sound like much, but in a high transaction environment, the slight performance hit will add up.

Also, consider what would happen if Microsoft decides to ship a system stored procedure with the same name as the procedure you wrote. Suddenly, your procedure will stop working and the one supplied by Microsoft will be executed instead. To see what I mean, try creating a stored procedure in your database named sp_help. When you execute this stored procedure, SQL will actually execute the one in the master database instead.

**How to detect this problem:**

```sql
Select	* 
From	Information_Schema.Routines 
Where	Specific_Name Like 'sp[_]%'
```
**How to correct it:** To correct this problem, you will need to identify all procedures named this way, and then change the name of the procedure. There are far greater implications though. Some stored procedures are called by other stored procedures. In cases like this, you will need to change those stored procedures too. Additionally, you will also need to change your front end code to call the procedure with the new name.

**Level of difficulty:** medium to high. The level of effort required to correct this problem can range from medium to high, depending on how many procedures you have than require a name change.

One possible strategy you could use to help resolve this problem would be to rename the procedure, and then create a procedure with the original name. This procedure could write to a log file, and then call the original procedure. This strategy allows your application to continue working (albeit a little slower because of the logging). You can then determine which application ran the procedure and change the name of the call.

**Level of severity:** Moderate