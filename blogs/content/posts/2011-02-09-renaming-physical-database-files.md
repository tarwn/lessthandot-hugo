---
title: Renaming Physical Database Files
author: Ted Krueger (onpnt)
type: post
date: 2011-02-09T22:22:00+00:00
ID: 1038
excerpt: 'Using ALTER DATABASE and MODIFY FILE you can reset the metadata in the master database for renaming physical mdf and ldf files. This however does not actually change the physical file name itself on disk. To fix that, you are typically required to change the metadata in master and then manually change the physical files on disk after taking the database offline.  Then bring the database online and the task is complete.'
url: /index.php/datamgmt/dbprogramming/renaming-physical-database-files/
views:
  - 5775
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
Using ALTER DATABASE and MODIFY FILE you can reset the metadata in the master database for renaming physical mdf and ldf files. This however does not actually change the physical file name itself on disk. To fix that, you are typically required to change the metadata in master and then manually change the physical files on disk after taking the database offline.  Then bring the database online and the task is complete.

To get around this manual process you could use CLR with External access set in a DBA database.

> <span class="MT_red">Note: DBAs should have a repository that stores all of their functions, procedures and other objects. This keeps your SQL Server clean and is easily created on new servers when they are built with scripts.</span> 

The CLR procedure to handle the change can be used off the model code below:

(Since this SQLCLR procedure uses System.IO, it requires External access)

```csharp
public partial class StoredProcedures
{
    [Microsoft.SqlServer.Server.SqlProcedure]
    public static void ChangeFileName(string oldmdf, string oldldf, string newmdf, string newldf)
    {
        try
        {
            SqlContext.Pipe.Send(oldmdf);
            SqlContext.Pipe.Send(oldldf);
 
            if (File.Exists(oldmdf))
            {
                File.Move(oldmdf, newmdf);
                SqlContext.Pipe.Send("File moved");
            }
            if (File.Exists(oldldf))
            {
                File.Move(oldldf, newldf);
                SqlContext.Pipe.Send("File moved");
            }
        }
        catch (SqlException ex)
        {
            SqlContext.Pipe.Send(ex.ToString());
        }
    }
};
```

Then to follow this you can run the following T-SQL Statement to execute the change to master and also change the physical file names 

sql
USE MASTER
Go
DECLARE @MDF NVARCHAR(1000)
DECLARE @LDF NVARCHAR(1000)
DECLARE @NEWMDF NVARCHAR(1000)
DECLARE @NEWLDF NVARCHAR(1000)
DECLARE @CMD NVARCHAR(2000)
SET @NEWMDF = N'C:Program FilesMicrosoft SQL ServerMSSQL10.MSSQLSERVERMSSQLDATAAdventureWorks_Data.mdf'
SET @NEWLDF = N'C:Program FilesMicrosoft SQL ServerMSSQL10.MSSQLSERVERMSSQLDATAAdventureWorks_Log.ldf'
SET @MDF = (SELECT physical_name FROM SYS.MASTER_FILES WHERE database_id = DB_ID('AdventureWorks') AND type_desc = N'ROWS')
SET @LDF = (SELECT physical_name FROM SYS.MASTER_FILES WHERE database_id = DB_ID('AdventureWorks') AND type_desc = N'LOG')
 
ALTER DATABASE AdventureWorks SET OFFLINE
 
EXEC master.dbo.ChangeFileName @MDF,@LDF,@NEWMDF,@NEWLDF
 
SET @CMD = ('ALTER DATABASE AdventureWorks
                        MODIFY FILE
                              ( NAME = N''AdventureWorks_Data'',
                                FILENAME = ''' + @NEWMDF + ''')')
EXEC(@CMD)
 
SET @CMD = ('ALTER DATABASE AdventureWorks
                        MODIFY FILE
                              ( NAME = N''AdventureWorks_Log'' ,
                                FILENAME = ''' + @NEWLDF + ''')')
EXEC(@CMD)
 
ALTER DATABASE AdventureWorks SET ONLINE
```

This is only valid for a database that has one mdf and one ldf.  To alter this to handle either multiple ldf or added ndf file, change the CLR procedure to simply change one at a time.  Alternately you could add in logic by passing the entire contents of the database sys.master_files and then based on the rows sent as the parameter, change accordingly.  Use this method as a guide to build it to your own flavor 🙂

 

 