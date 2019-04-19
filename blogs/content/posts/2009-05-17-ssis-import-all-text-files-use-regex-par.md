---
title: SSIS Import all text files. Use RegEx parse out strings
author: Ted Krueger (onpnt)
type: post
date: 2009-05-17T20:53:39+00:00
ID: 432
url: /index.php/datamgmt/datadesign/ssis-import-all-text-files-use-regex-par/
views:
  - 18104
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
I'm spawning off a thread that I helped out in on SQLServerCentral for this article. The basic need in the task is, import a bunch of text files, parse out all email address and insert them delimited by “;” into a relational table.

This is going to cover a few common things you will need to know how to do in order to successfully build ETL tasks.

  * Use regular expression matches in SSIS transforms, parse them and BULK load them into tables. 
  * Import a directory of files using the SqlBulkCopy method in Script Tasks. 
  * Use Script Component transformations type asynchronously. 

I'm going to show how to import a text file into a varchar(max) field although I think the better choice in performance and maintainability is to have the path to the file stored as a pointer and leave the files as is in the directory system. Many cases, DBAs are not very fond of storing file contents in databases as it causes performance issues and the data types need to be large enough to handle the, “big ones”. This means planning and choosing a data type to optimize storage is difficult. 

There is also a requirement to know a few ways of reading text files into tables even if not used here.

One is BULK INSERT in T-SQL for reading each line.

```sql
CREATE TABLE #temp (contents varchar(max))

BULK INSERT #temp
   FROM 'C:resumesresume.txt';
GO

SELECT contents FROM #temp
```
OPENROWSET is another for reading the lot of the data

```sql
SELECT *
FROM OPENROWSET (BULK 'C:resumesresume.txt', SINGLE_CLOB) ResFile
```
BCP and a few others out of scope. SSIS is how we'll do this one though and future usage of scheduled jobs to keep the tables up to date.
  
Here are the supporting tables for this exercise

```sql
CREATE TABLE ResumeText (
		ident int identity(1,1) primary key,
		contents varchar(max),
		processed smallint
		)
Go
CREATE TABLE ResumeDetail (
		Resume_ident int,
		emails varchar(500)
		)
Go
ALTER TABLE ResumeDetail WITH CHECK ADD CONSTRAINT fk_ResumeText FOREIGN KEY(Resume_ident)
REFERENCES ResumeText(ident)
Go
```
First step in our SSIS package is to create your import task. Create a variable named ResumeFilePath and give it a location local to you machine for testing. I made mine C:Resumes. Bring over a script task to the control flow tab. Open up the task and add the variable to your read only variables. No other configurations other than the coding are needed for the task. Open the designer, “edit script” and paste the following code into it.

```CSHARP
using System;
using System.Data;
using Microsoft.SqlServer.Dts.Runtime;
using System.Windows.Forms;
using System.IO;
using System.Data.SqlClient;

namespace ST_213de4a3e24e4d84a5d9acb1e9a01ca8.csproj
{
    [System.AddIn.AddIn("ScriptMain", Version = "1.0", Publisher = "", Description = "")]
    public partial class ScriptMain : Microsoft.SqlServer.Dts.Tasks.ScriptTask.VSTARTScriptObjectModelBase
    {

        #region VSTA generated code
        enum ScriptResults
        {
            Success = Microsoft.SqlServer.Dts.Runtime.DTSExecResult.Success,
            Failure = Microsoft.SqlServer.Dts.Runtime.DTSExecResult.Failure
        };
        #endregion

        public void Main()
        { 
            string path = Dts.Variables["User::ResumeFilePath"].Value.ToString();
            string[] files = Directory.GetFiles(path,"*.txt");
            SqlBulkCopy bulkCopy = new SqlBulkCopy("Server=(local);Database=DBA05;Trusted_Connection=True;");

            bulkCopy.DestinationTableName = "dbo.ResumeText";

            DataTable dt = new DataTable();
            dt.Columns.Add(new DataColumn("contents"));

            foreach(string f in files)
            {
                DataRow row = dt.NewRow();
                row[0] = File.ReadAllText(f);
                dt.Rows.Add(row);
                dt.AcceptChanges();
            }

            try
            {
                bulkCopy.BatchSize = dt.Rows.Count;
                bulkCopy.ColumnMappings.Add("contents", "contents");

                bulkCopy.WriteToServer(dt);
            }
            catch (Exception)
            {
                Dts.TaskResult = (int)ScriptResults.Failure;
            }
            finally
            {
                bulkCopy.Close();
                Dts.TaskResult = (int)ScriptResults.Success;
            }
        }
    }
}
```
The basics of this script are to first grab the variable value so you know which directory to read from. Then start the bulk copy task by creating a data table and populating it with the file contents of each file. My first warning and possible reason to use other bulk load methods is the memory consumption you are going to use here. In this case we're dealing with text files and the need for GC to start kicking in are probably small. If you start working with word and pdf files you will more than likely move to an inline BULK INSERT or other SSIS proven tasks for each file while disposing the previous. This will keep memory consumption down. I take having a job server serious so ask for them. In this case the job server is stacked high on resources for heavy tasks like this. Every situation calls for testing on performance no matter what of course. I cannot stress enough for the need to do this. Just because it works, does not mean it should be done that way. Just because you know how to do it, doesn't mean you should hit the enter button either. There is a reason there are so many ways to do the same task and so many different platforms and languages to do them. If one thing was the best, life would be simple. Then again everyone would be a developer or DBA and we'd be paid the same all around so be thankful you have to think and make the decisions that keep the business alive. 

Now that the script task is completed, we can start working on the data flow task to parse the email addresses out of the text files that we just bulk loaded into the ResumeText table. Bring over a Data Flow Task and go into the Data Flow tab. I used an OLEDB source in this case. The table for the source is ResumeText and all columns are consumed. Use a Sql Command to grab the data or you'll reprocess and cause data integrity issues. 

The select would be in our case

```sql
SELECT 
	contents
	,ident
	,processed 
FROM 
	dbo.ResumeText 
WHERE processed = 0
```
In a real life situation, be sure to configure your error output always. Now bring over a script component and when prompted, select transformation component type. Connect the flow from the source to this script component. There are a few things that need to be done in the script components input and output columns before coding it. Select Input Columns and tick the columns from the input we see from the source. Make sure these are ReadOnly. Go to Input and Outputs next. Add two output columns. Name them, emailout and identout. Use DT\_I4 for the ident sense we used INT for the seed and key. Then all we need out for this task is the email string so use DT\_STR as the type for the emailout type. Highlight Ouput 0 again and change SynchronousInputID to None. Go back to the script section and click the edit script button to open the designer. The following code is pretty self explaining as the first script task we created was. The first thing we do is find all the emails in the contents column and build a string delimited by “;”. This returns and then builds the new row for inserting into the ResumeDetail table while retaining the key. The regular expression patter is a typical email pattern. There are dozens of these out there on the net. I would try a few and see which fits your requirements the best. One thing you'll see in the code is, I set the email column to “No emails found” if there are none. I wouldn't do this in real life and would use a Boolean value to keep with using the correct data type per need. In this example I'm testing only so if you use this, please change this so you do not cause senseless performance gains by misusing data types in the table designs.

```CSHARP
using System;
using System.Data;
using Microsoft.SqlServer.Dts.Pipeline.Wrapper;
using Microsoft.SqlServer.Dts.Runtime.Wrapper;
using System.Text.RegularExpressions;

[Microsoft.SqlServer.Dts.Pipeline.SSISScriptComponentEntryPointAttribute]
public class ScriptMain : UserComponent
{

    public override void PreExecute()
    {
        base.PreExecute();
    }

    public override void PostExecute()
    {
        base.PostExecute();
    }

    public override void Input0_ProcessInputRow(Input0Buffer Row)
    {
        string m = ValidateEmailRow(Row.contents.ToString());
        
        if (m.Length > 0)
        {
            Output0Buffer.AddRow();
            Output0Buffer.emailout = m;
            Output0Buffer.identout = Row.ident;
        }
        else
        {
            Output0Buffer.AddRow();
            Output0Buffer.emailout = "No emails found";
            Output0Buffer.identout = Row.ident;
        }
    }


    public string ValidateEmailRow(string email)
    {
        string emails = "";
        Regex reg = new Regex(@"b([a-zA-Z0-9._%+-]+)@([a-zA-Z0-9.-]+.[a-zA-Z]{2,4})b");
        Match validate = reg.Match(email);
        while (validate.Success)
        {
            emails += validate.Value.ToString() + ";";
            validate = validate.NextMatch();
        }
        return emails;
    }
}
```
The last steps are to map the destination and update our processed flag indicator. In an OLEDB destination, select our ResumeDetail table and then in the mappings, connect the emailout to emails and identout to resume_ident. 

Back in the control flow tab, bring over an Execute SQL Task and use the connection already created to your instance. Use the following statement then to update the flag

```sql
Update ResumeText 
Set Processed = 1
WHERE Processed = 0
```
You can also use a multicast object in the data flow tab and task to split the data to another component for updating. In this case I used a clear update to force trigger firing in later possible data manipulations. 

Save everything just in case BIDS locks on you and go ahead and run it all.
  
Hope you had the green boxes I did 🙂