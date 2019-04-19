---
title: Automate Database Restore to Remote Instance with SSIS
author: Ted Krueger (onpnt)
type: post
date: 2011-03-30T18:32:00+00:00
ID: 1094
excerpt: |
  Automate Database Restore to Remote Instance with SSIS
   
  This past weekend I presented a new session titled SSIS: The DBA Multipler.  The session covered taking the power of SSIS and extending the SQL Server Agent with it in order to multiply and auto&hellip;
url: /index.php/datamgmt/dbprogramming/automate-database-restore-to-remote/
views:
  - 42352
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
  - SSIS

---
**To follow this article, download [Refresh Development.zip][1] and add it to a new or exisitng SQL Server 2008 Integration Services Project in BIDS.** 

This past weekend I presented a new session titled SSIS: The DBA Multipler.  The session covered taking the power of SSIS and extending the SQL Server Agent with it in order to multiply and automate DBA tasks.  The session went very well and I received a lot of great feedback.  Thank you again to all that attended.

In the session I went into an SSIS package that is close to something I have used for automating development restores.  There is much manual effort in restoring production database to development and this task over the years was one I humored many methods for automation.  I've recently written PowerShell scripts to do the same process and SSIS still seems to be more stable for this process.

There are some key variables that need to be addressed before blindly running an automated restore over a database.  The primary is: can you overwrite the database in development to begin with?  Development work that is not stored in source control and easily deployed back to a database can result in lost work.  That is the last thing our automation should cause.  Why automate if it creates longer and more work for us?  Yes, even if it creates more work for the developers.

If development work is in something like Team Foundation Server (TFS) or Subversion, you can deploy the database scripts back to the database after the restore is done.  You can also use something such as Red Gate's compare to automate the backup of changes and then apply them once the restore is done.  There are many options in that step to prevent loss of work.  Asking if loss of work is a possibility is the main objective before starting this automated process.

 

 

**The main process flow**

The main processing flow of this package starts with determining where the files are and moving them to a safe place to restore them.  Once this is accomplished the conditional settings are followed to either start a full restore or full and differential restore.  After all the processing is performed and successful, maintenance such as changing to Simple recovery or CHECKDB is performed.

This would appear in a flowchart as shown in figure 1.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/All/-15.png?mtime=1301516186"><img alt="" src="/wp-content/uploads/blogs/All/-15.png?mtime=1301516186" width="548" height="629" /></a>
</div>

Figure 1

 

 

**Script Task and File System Task**

The Script Task is a powerful task that allows us to place C# or VB.NET code directly into our SSIS packages.  This ability creates an endless set of possibilities that we can accomplish from SSIS.  With this ability, thought should be used so the Script Task is not abused and causes a negative impact on our processing.   

The Script Task in this package is used to allow for a more intuitive search by patterns for the correct backup file.  The search handles a pattern of mmddyyyy and FULL and DIFF.  The date pattern is first used so we extract and move only the date that we wish to restore.  The FULL and DIFF signifies the type of backup that the file contains. 

If file naming conventions are not set in your environment, it is highly recommended to do so. 

The development skills that are required for writing a Script Task such as this are very minimal.  Below is the code for the task that is used.  The flow of this code is as follows:

Extract all file names in a folder

Check each file for the file required

Set variables accordingly based on findings

Log either success or failures

```csharp
try
            {
                string backup_folder = Dts.Variables["SourceBackupFolder"].Value.ToString();
                string GetFileDate = Dts.Variables["mmddyyyy"].Value.ToString();
                byte[] emptyBytes = new byte[0];

                DirectoryInfo dir = new DirectoryInfo(backup_folder);

                foreach (FileInfo file in dir.GetFiles())
                {
                    if (file.Name.IndexOf(GetFileDate) != -1 && file.Name.IndexOf("FULL") != -1)
                    {
                        Dts.Variables["FullBackup"].Value = file.Name;
                    }
                }

                if (Dts.Variables["ApplyDiff"].Value.ToString() == "1")
                {
                    foreach (FileInfo file in dir.GetFiles())
                    {
                        if (file.Name.IndexOf(GetFileDate) != -1 && file.Name.IndexOf("DIFF") != -1)
                        {
                            Dts.Variables["DiffBackup"].Value = file.Name;
                        }
                    }
                }
                Dts.Log("Successfully set variables for Full or Differential distinction: Backup folder=" + backup_folder + " Pattern search=" + GetFileDate + " Full Backup File=" + Dts.Variables["FullBackup"].Value.ToString() + " Differential backup File=" + Dts.Variables["DiffBackup"].Value.ToString(), 0, emptyBytes);
                Dts.TaskResult = (int)ScriptResults.Success;
            }
            catch (Exception ex)
            {
                Dts.Events.FireError(0, "Error setting Full or Differential variables", ex.Message + "r" + ex.StackTrace, String.Empty, 0);
                Dts.TaskResult = (int)ScriptResults.Failure;
            }
        }
```

 

**Variables**

Automating the restoring of a database to remote instances brings with it the need for many dynamic values to be set.  Variables in SSIS allowed these dynamic needs to be accomplished.  With variables set at run-time and constants, we can use Expressions in properties for all the tasks that are to be performed to obtain dynamically, the values we need. 

This dynamic process makes the package mobile and reusable.

With the package we are building, the variables outlined below are required.  ApplyDiff will be the only variable that is an Int32 and not a string in the listing.

  * ApplyDiff: 1 = Differential should be applied, 0 = No differential is to be applied
  * DatabaseName: The database name that is stored in the backup header. Used for logical database name
  * DatabaseNameRestore: The database name that will be used to restore as. Logical restored name
  * DataFile: Logical data file name
  * LogFile: Logical transaction log file name
  * DestBackupFolder: Where backup files will be moved to before restoring
  * DiffBackup: Dynamically set from script task when differential file is found
  * DiffBackupPath: Full path set by expression of other variables (path and file name) for differential backup
  * DiffRestorePath: Full path set by expression of other variables (path and file name) for differential restore
  * FullBackup: Dynamically set from script task when full backup file is found
  * FullBackupPath: Full path set by expression of other variables (path and file name) for full backup
  * FullRestorePath Full path set by expression of other variables (path and file name) for full restore
  * RestoreLocMDF: restore location that the MOVE option will use for MDF file
  * RestoreLocLDF: restore location that the MOVE option will use for LDF file
  * ServerName: SQL Server instance that is connected to for restore. Default to master 
  * connection. Requires backup operator or sys admin roles
  * SourceBackupFolder: Where backup files are located

**Expressions**

As stated in the Variables section; Expressions are used in several areas for this task in order to allow for mobility and reusability of the package.  Creating the package to run one distinct restore on a specific database is fine, but we'd want to prevent work hours wasted on changing hard-coded values later when we want to expand the use.

The variables, DiffBackupPath, DiffRestorePath, FullBackupPath and FullRestorePath are the first evaluated variables we will look at.

These variables use the SourceBackupFolder and DiffBackup or FullBackup to build the absolute path to the files.  This is done in the variable under the expression property with the following expression:

> @[User::SourceBackupFolder] + @[User::DiffBackup]</p>
Ensure that any variable that uses an expression property also has the EvaluateAsExpression set to True.

The variables that are evaluating to the absolute path are then used in the File System Tasks.  This allows for the folder and file name to change freely by just changing one or two variables.  This also requires the File System Tasks to be set to use a variable by using the IsDestinationPathVariable and IsSourcePathVariable settings. 

<div class="image_block">
  <a href="/wp-content/uploads/blogs/All/-16.png?mtime=1301516186"><img alt="" src="/wp-content/uploads/blogs/All/-16.png?mtime=1301516186" width="519" height="246" /></a>
</div>

The next expression usage will focus on the RESTORE command itself.  Giving the RESTORE command some thought, we see that there are many values that should be passed in order to successfully restore the database.  These values include the logical data and log names, the physical data and log file paths, the backup file itself and most importantly, the database name in which we want to restore the backup as.

Taking one of the Execute T-SQL Tasks for example, an expression is used to utilize variables that have been created to hold all of these variables.  The expression for this is set on the SqlStatementSource.  This allows for the expression to be evaluated and a resulting string to be set as the SQL statement that will be executed from the task.

The expression for the task to restore the differential backup file:

> “RESTORE DATABASE [” + @[User::DatabaseNameRestore] + “] FROM DISK = N'” + @[User::DiffRestorePath] + “' ” +
  
> “WITH FILE = 1, ” +
  
> “MOVE N'” + @[User::DataFile] + “' TO N'” + @[User::RestoreLocMDF] + “', ” +
  
> “MOVE N'”+ @[User::LogFile] + “' TO N'” + @[User::RestoreLocLDF] + “', ” +
  
> “NOUNLOAD, ” +
  
> “REPLACE, STATS = 10 “</p>
 

Evaluated, this expression results in:

```text
RESTORE DATABASE [DBA_DEV] FROM  DISK = N'c:restoresFULL_12122001.BAK' WITH  FILE = 1,  MOVE N'DBA' TO N'C:SQL2008R2MSSQL10_50.TK2008R2MSSQLDATADBA_DEV.mdf', MOVE N'DBA_LOG' TO N'C:SQL2008R2MSSQL10_50.TK2008R2MSSQLDATADBA_DEV_1.ldf',  NOUNLOAD, REPLACE,  STATS = 10
```
SqlStatementSource values are exclusively used on all the T-SQL Tasks in this package.  Again, this allows us to freely change these values at run-time so we can reuse the package on several different database or configurations.

 

 

**Precedence**

The Precedence Constraints in SSIS have a normal use of success and failure.  These links also have other options available that can assist in controlling the flow of a package and making conditional choices on the next executed task, container or component. 

In particular, the evaluation of an Expression in the constraint is used to control if the ApplyDiff variable is set to a value that requires the package to restore with NORECOVERY in order to apply a differential backup. 

This is accomplished by setting either one condition to True or both conditions to True.  With these constraints, you can also change the behavior of a logical AND to a logical OR causing more than one constraint to fire based on the evaluation of the expression.

The expression to validate if a differential is required and to execute the differential restore statements is:

> @[ApplyDiff] == 1 ? True : False</p>
<div class="image_block">
  <a href="/wp-content/uploads/blogs/All/-17.png?mtime=1301516187"><img alt="" src="/wp-content/uploads/blogs/All/-17.png?mtime=1301516187" width="624" height="454" /></a>
</div>

**Logging**

Logging is another critical setting and configuration to perform in all SSIS packages.  Output from the SQL Agent or from the execution of an SSIS package can be limiting for help when a failure is found.  With logging and event handlers, detailed information can be compiled into one or more sources so you can troubleshoot failures quickly.

Setup logging by configuring an SSIS log provider for Text files.  Have this file write to a share or for test purposes, the C Drive in the package created in this article. 

Set the primary tasks to log errors and information.  Set the script tasks to log errors and script task log entry.  Set the file system task to log file system operations.

Within the script task that searches for the files and also set the variables, log entries should be sent so failures can be reviewed and also placement holders can be logged.  The code in the Script Task section has the following lines that accomplish this:

For informational logging:

```csharp
Dts.Log("Successfully set variables for Full or Differential distinction: Backup folder=" + backup_folder + " Pattern search=" + GetFileDate + " Full Backup File=" + Dts.Variables["FullBackup"].Value.ToString() + " Differential backup File=" + Dts.Variables["DiffBackup"].Value.ToString(), 0, emptyBytes);
```


 

For error logging:

```csharp
Dts.Events.FireError(0, "Error setting Full or Differential variables", ex.Message + "r" + ex.StackTrace, String.Empty, 0);
```


 

**Package and conclusion**

The package that is attached for download to this short explanation is useful for a building foundation to matching your own restore needs.  Each task can be used to create more ideas for automation and multiplying your own DBA headcount.

With the current DB teams shrinking and the need for less to do more, finding the tasks that are manually performed and using tools like SSIS, PowerShell or even writing custom executables and services, is very valuable. 

Take the time to follow yourself through a normal week of database administration and see just how much you can automate.

**Using the attached DTSX**

First, download the attached zip file. This file contains a DTSX file that is the package explained from this article. To use the DTSX file available here for download, add it to an existing or new BIDS SQL Server Integration Services project.  Change the variables for the restore, backup and instance name paths as well as the database name.  Please comment here if you have any problems with it or bug reports on this stripped down version.

 

 [1]: /wp-content/uploads/blogs/All/Refresh Development.zip?mtime=1301517046