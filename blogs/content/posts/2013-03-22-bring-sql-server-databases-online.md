---
title: Bring SQL Server databases Online or Offline when running on Amazon RDS
author: SQLDenis
type: post
date: 2013-03-22T10:25:00+00:00
ID: 2049
excerpt: |
  To bring a SQL Server database online or offline you can use a command like the following if your database is named test.
  
  ALTER DATABASE test SET OFFLINE;
  
  ALTER DATABASE test SET ONLINE;
  
  When running SQL Server on Amazon's Relational Database S&hellip;
url: /index.php/datamgmt/dbadmin/bring-sql-server-databases-online/
views:
  - 27315
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - amazon web services
  - aws
  - cloud
  - rds

---
To bring a SQL Server database online or offline you can use a command like the following if your database is named test.

sql
ALTER DATABASE test SET OFFLINE;

ALTER DATABASE test SET ONLINE;
```

When running SQL Server on Amazon&#8217;s Relational Database Service it is done a little different. While you can use the command above to take the database offline, you can&#8217;t use the command to bring the database online.

I you have a database name test and you execute the following it will work

sql
ALTER DATABASE test SET OFFLINE
```

You can verify this by running the following

sql
USE test
GO
```

You get the following error

_Msg 942, Level 14, State 4, Line 1
  
Database &#8216;test&#8217; cannot be opened because it is offline._

If you try running the following command to bring the database online

sql
ALTER DATABASE test SET ONLINE;
```

Now you get this error

_Msg 5011, Level 14, State 9, Line 1
  
User does not have permission to alter database &#8216;test&#8217;, the database does not exist, or the database is not in a state that allows access checks.
  
Msg 5069, Level 16, State 1, Line 1
  
ALTER DATABASE statement failed._

So what can you do? RDS has a special stored procedure that will bring the databse online, the name of this stored procedure is `rdsadmin.dbo.rds_set_database_online`

Here is how you use it

sql
EXEC rdsadmin.dbo.rds_set_database_online  'test'
GO
```

Now you can use the test database again

sql
USE test
GO
```

See, simple, you just have to be aware of these differences