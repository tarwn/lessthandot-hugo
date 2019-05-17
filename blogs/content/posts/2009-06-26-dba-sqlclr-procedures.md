---
title: DBA SQLCLR Procedures
author: Ted Krueger (onpnt)
type: post
date: 2009-06-26T10:57:12+00:00
ID: 483
url: /index.php/datamgmt/dbprogramming/dba-sqlclr-procedures/
views:
  - 5373
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server

---
SQLCLR adds a great deal to the SQL Developer list of methods to get the job done. That is pretty much a given. Hardware, resource usage and all that endless discussion aside, SQLCLR given handled correctly and given the hardware to utilize its abilities without putting memory and other pressure on your instances can be truly a beneficial aspect to your bag of tricks.

As a DBA I have dozens of SQLCLR procedures that make my life unbelievably easier than in prior years. Also coming from years of developer experience have helped me along in catching on the SQLCLR. Today I thought sharing a few common and very useful DBA procedures would make a handy post.

The first is DateTime formatting. Yesterday I posted on [SSRS internal procedures][1] In that post I gave up a handy SQLCLR procedure for formatting a DateTime value to handle Time Zone Offset. I use this one often in DBA tasks and draw off of it for many things.

Here is that method again (this is also a UDF and not a Proc)

```csharp
public partial class UserDefinedFunctions
{
    [Microsoft.SqlServer.Server.SqlFunction]
    public static SqlString DateTimeTimeZoneOffset(DateTime datetime_sent)
    {
        return new SqlString(datetime_sent.ToString("yyyy-MM-ddTHH:mm:ss.fffzzzz"));
    }
};
```
Call

```sql
Select dbo.DateTimeTimeZoneOffset('2009-06-26')
```
Usage of this is seen directly in the blog post from yesterday and how it can be a simply task for formatting things like DateTime. Don't let it stop there though. Formatting strings for file names, headers, logs and on can be done quickly and easily with C# (or VB.NET) and makes this a great tool to any DBA.

Next goes into file operations.

The first is file movement. This can be done with external objects like batch, ActiveX scripts and even executables called via the OS command in jobs. Using this as a SQLCLR procedure makes it nice in calling the operation directly from the TSQL task you are performing though.

Move file (which is also the rename file method)

```csharp
public partial class StoredProcedures
{
    [Microsoft.SqlServer.Server.SqlProcedure]
    public static void MoveFile(string sfilename, string dfilename)
    {
        try
        {
            File.Move(sfilename, dfilename);
        }
        catch (Exception ex)
        {
            SqlContext.Pipe.Send("Error writing to file : " + ex.Message);
        }
    }
};
```
Call

```sql
Exec MoveFile 'C:txt_doc.txt','C:txt_doc_new.txt'
```

Write file

```csharp
public class SQLCLRIO
{

    public static void WriteToFile(String content, String filename)
    {
        try
        {
            File.WriteAllText(filename, content);
        }
        catch (Exception ex)
        {
            SqlContext.Pipe.Send("Error writing to file : " + ex.Message);
        }
    }
}
```
Call

```sql
Exec WriteToFile 'Testing....','C:txt_doc_new.txt'
```

Last one I'll post today is removing old files (backups typically) which is similar to a task I used to do using vbs scripts. The vbs scripts are a good method. I still use it on a few instances I want security more restricted as well. I say that of course as the permission level of any file operations SQLCLR will be external or unsafe. Given that setting is permitted in the situation, the SQLCLR is handy and easier to throw logging and other methods into the process.

Delete old backups in a series of subdirectories. 

```csharp
public partial class StoredProcedures
{
    [Microsoft.SqlServer.Server.SqlProcedure]
    public static void RemoveOldBackups(string path,int retention)
    {
      try
            {
            DirectoryInfo dirList = new DirectoryInfo(path);
            DirectoryInfo[] subs;
            System.TimeSpan diff;

            subs = dirList.GetDirectories();

            foreach (DirectoryInfo dir in subs)
            {
                foreach (FileInfo file in dir.GetFiles())
                {
                    diff = DateTime.Now.Subtract(file.LastWriteTime);
                    if (diff.Hours > retention)
                    {
                        file.Delete();
                    }
                }
            }
        }
        catch (Exception ex)
        {
            SqlContext.Pipe.Send("Error deleting from diretory : " + ex.Message);
        }
    }
};
```
Call

```sql
Exec RemoveOldBackups 'C:test',23
```

Have fun and don't forget the pressure SQLCLR can bring to you instances.



\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][2] forum or our [Microsoft SQL Server Admin][3] forum**<ins></ins>

 [1]: /index.php/DataMgmt/DataDesign/not-a-fan-of-the-report-manager-in-ssrs-
 [2]: http://forum.lessthandot.com/viewforum.php?f=17
 [3]: http://forum.lessthandot.com/viewforum.php?f=22