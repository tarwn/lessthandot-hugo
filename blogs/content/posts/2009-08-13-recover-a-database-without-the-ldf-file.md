---
title: Recover a database without the ldf file
author: Ted Krueger (onpnt)
type: post
date: 2009-08-13T10:54:58+00:00
ID: 538
url: /index.php/datamgmt/dbprogramming/recover-a-database-without-the-ldf-file/
views:
  - 50901
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
Test case: SQL Server 2000 and up in Full recovery model. Simple recovery will not reacte this way as the log is utilized differently by SQL Server based on the recovery models.

Thanks to [@DenisGobo][1] and [@bonskijr][2] for the great conversation and test cases on this topic. 

Again today I read a few threads and even saw on twitter where a person (DBA or not) was in panic mode because they had lost the ldf file for a database. One of the answers was the steps that involved a SQL Server restart. Personally that's not an option. If your landscape is setup as most are, then simple restarts means you take down several data sources and not just the one in suspect. There really is a simple solution to the problem. Don't delete the damn ldf file! It's important you know. Kind of has a critical part to how the database and SQL Server functions. OK, that was my stab at, “How the hell did that happen in the first place?” Seriously though, there is a nicely wrapped system procedure for this very scenario. It's called, “sp\_attach\_single\_file\_db”. The name says it all. Attach a database by means of the mdf file only. So let's do it in a trial...

First run this on a local or development instance

sql
CREATE DATABASE [LOGLOSS] ON PRIMARY
( NAME = N'LOGLOSS', FILENAME = N'C:Program FilesMicrosoft SQL ServerMSSQL.1MSSQLDATALOGLOSS.mdf' , SIZE = 3048KB , MAXSIZE = UNLIMITED, FILEGROWTH = 1024KB )
LOG ON
( NAME = N'LOGLOSS_log', FILENAME = N'C:Program FilesMicrosoft SQL ServerMSSQL.1MSSQLDATALOGLOSS_log.ldf' , SIZE = 1024KB , MAXSIZE = 2048GB , FILEGROWTH = 10%)
GO
ALTER DATABASE [LOGLOSS] SET RECOVERY FULL
GO
```
Now run this little gem of a statement...

sql
ALTER DATABASE LOGLOSS SET OFFLINE
```

Now go to the directory you created the DB in and delete the LOGLOSS_log.ldf
  
First let's see how bad that really made things for us. Run this...

sql
ALTER DATABASE LOGLOSS SET ONLINE
```

You'll soon see we've successfully blown up our database. 

```
File activation failure. The physical file name "C:Program FilesMicrosoft SQL ServerMSSQL.1MSSQLDATALOGLOSS_log.ldf" may be incorrect.
Msg 945, Level 14, State 2, Line 1
Database 'LOGLOSS' cannot be opened due to inaccessible files or insufficient memory or disk space.  See the SQL Server errorlog for details.
Msg 5069, Level 16, State 1, Line 1
ALTER DATABASE statement failed.
```

All is not lost. Let's recover. One thing that is very important to note is anything that was in that log that was not committed is gone. After this recovery is completed, you're next big task that no one here can really help with, is to validate your data and or more importantly, loss of data. Always run a DBCC CHECKDB on that recovered database to find errors and fix them as well. Update usage, rebuild indexes and on before you release the thing to production. And most important, back the mdf up before you start messing with it for recovery. If you corrupt the mdf to the point it is not recoverable in the process of trying to recover, you want to be able to start from scratch again.

So first, [here][3] is the documentation of the procedure we need.

```
sp_attach_single_file_db [ @dbname= ] 'dbname'
        , [ @physname= ] 'physical_name'
```

This is pretty basic at that. It comes down to the following in our test case

sql
sp_attach_single_file_db @dbname='LOGLOSS'
        ,@physname=N'C:Program FilesMicrosoft SQL ServerMSSQL.1MSSQLDATALOGLOSS.mdf'
```

Before we can do this however, we need to rid our Meta data of the existence of the database LOGLOSS in the first place. In our test case we took the database offline. Sense we did that, the engine won't allow an attachment as it is already there. So we need to remove that listing all together. Here is where that backup of the mdf. If you didn't do that, you better now because we're about to delete it.
  
So if you haven't, go into the Data directory and copy the mdf to the backup directory.
  
Now run the DROP statement as

sql
DROP DATABASE LOGLOSS
```

If the DROp has removed the data file, go ahead and copy it back. If it did not as the method of DROP while the DB is offline may not, then let all is good.
  
Now let's attach the LOGLOSS database again

Running

sql
sp_attach_single_file_db @dbname='LOGLOSS'
        ,@physname=N'C:Program FilesMicrosoft SQL ServerMSSQL.1MSSQLDATALOGLOSS.mdf'
```

Results in...

```
File activation failure. The physical file name "C:Program FilesMicrosoft SQL ServerMSSQL.1MSSQLDATALOGLOSS_log.ldf" may be incorrect.
New log file 'C:Program FilesMicrosoft SQL ServerMSSQL.1MSSQLDATALOGLOSS_log.LDF' was created.
```

Yay!!!

Don't forget to checkdb the thing

sql
DBCC CHECKDB('LOGLOSS')
```

In our test case you should get the happy days of

```
CHECKDB found 0 allocation errors and 0 consistency errors in database 'LOGLOSS'.
```

Congratulations, you just recovered from some fool deleting your LDF file or in the event of disk loss. Just remember, anything not committed is gone. Without the original there is no recovery from that.

 [1]: http://twitter.com/DenisGobo
 [2]: http://twitter.com/bonskijr
 [3]: http://technet.microsoft.com/en-us/library/ms174385.aspx