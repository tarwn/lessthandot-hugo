---
title: Removing old files with SSIS
author: Ted Krueger (onpnt)
type: post
date: 2010-06-04T12:29:42+00:00
ID: 809
excerpt: 'This post will illustrate two methods for removing old files from directories using SSIS.  This task is often used to delete old backup files and other ETL files that are not required any longer.  We’ll step through two methods.  First method uses a script task entirely for the removal and the logging events.  This method will also have some comments in for logging and using the FireInformation method to mimic the normal logging abilities of SSIS.  The FireInformation method didn’t provide much more of a performance boost so it wasn’t used here.  Second method uses a Foreach Loop Container, Script Task for logic and a File System Task for the delete event.  SSIS Logging will be utilized with the OnPreExecute and OnPostExecute events in the second method over the System.IO method of AppendText.'
url: /index.php/datamgmt/dbprogramming/removing-old-files-with-ssis-sql-server/
views:
  - 23280
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - delete files
  - sql community
  - sql server
  - sql server 2008
  - sql server integration services
  - ssis

---
This post will illustrate two methods for removing old files from directories using SSIS. This task is often used to delete old backup files and other ETL files that are not required any longer. We’ll step through two methods. First method uses a script task entirely for the removal and the logging events. This method will also have some comments in for logging and using the FireInformation method to mimic the normal logging abilities of SSIS. The FireInformation method didn’t provide much more of a performance boost so it wasn’t used here. Second method uses a Foreach Loop Container, Script Task for logic and a File System Task for the delete event. SSIS Logging will be utilized with the OnPreExecute and OnPostExecute events in the second method over the System.IO method of AppendText. 

The use of System.IO in a script task performed much better than the use of the File System Task. 

Both methods were tested on folders containing 300 files with size ranging from 200MB to 1GB. 168 files were placed among the 300 in order to meet the properties that will require a delete event to fire.

Deletion by System.IO and manually logging to flat files elapsed in 156 Milliseconds. 

Deletion by means of a Foreach Loop Container containing a Script Task to validate the file meets criteria (File System Task is limited in doing that) and then the File System Task to force the delete took a whopping 3.962 seconds!

Have fun playing with this yourself and hope it comes in handy when you need to perform this type of task. 

If anyone has other methods or improvements to these, please feel free to comment or start a thread in the LessThanDot SQL Server forums. 

## Method one: Script Task

  1. Bring over a Script Task into the Control Flow 
<div class="image_block">
  <img src="/wp-content/uploads/users/onpnt/ssis_delete_1.gif" alt="" title="" width="437" height="157" />
</div>

  2. Name the task, “Remove all files based on backdays”
  3. Create the following variables
<div class="image_block">
  <img src="/wp-content/uploads/users/onpnt/ssis_delete_2.gif" alt="" title="" width="343" height="137" />
</div>

  4. Double click the script task to open the editor.
  5. Add the variable to the ReadOnlyVariables area
<div class="image_block">
  <img src="/wp-content/uploads/users/onpnt/ssis_delete_3.gif" alt="" title="" width="585" height="410" />
</div>

  6. Click Edit Script
  7. Add the following code
```csharp
using System;
using System.IO;
using System.Data;
using Microsoft.SqlServer.Dts.Runtime;
using System.Windows.Forms;

namespace ST_aaf20a0bd5b94ad394ab80cf5d585c41.csproj
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
            try
            {
                string logfile = Dts.Variables["User::logfile"].Value.ToString();
                DateTime olderThanDate = (DateTime)Dts.Variables["User::daysback"].Value;
                string folder = Dts.Variables["User::folder"].Value.ToString();

                    if (!(File.Exists(logfile)))
                    {
                        FileInfo fi = new FileInfo(logfile.ToString());
                        FileStream fstr = fi.Create();
                        fstr.Close();
                    }
                    ActionJackson(folder, logfile, olderThanDate);
            }
            catch (Exception Ex)
            {
                Dts.Events.FireError(1, "", "BOOM!!!  " + Ex.Message.ToString(), "", 0);
            }

            Dts.TaskResult = (int)ScriptResults.Success;
        }

        private void ActionJackson(string folder, string path, DateTime olderThanDate)
        {
                DirectoryInfo dirInfo = new DirectoryInfo(folder);
                TextWriter tw = File.AppendText(path);

                FileInfo[] files = dirInfo.GetFiles();
                foreach (FileInfo file in files)
                {
                    if (file.LastWriteTime < olderThanDate)
                    {
                        //or use Dts.Events.FireInformation(0, "", "File Deleted Succesfully", "", 0, True)
                        //with a logging file destination setup
                        //the two methods did not vary in performance much.  FireInformation allows for 
                        //the same descriptive logging as OnPreExecute/OnPostExecute descriptions 
                        tw.WriteLine("File " + file.FullName.ToString() + " Deleted on " + System.DateTime.Now.ToString());
                        file.IsReadOnly = false;
                        file.Delete();
                    }
                }
                tw.Close();
        }
    }
}
```

  8. Close the code editor and click OK to the script task editor.
  9. Right click an empty space in the Control Flow and select Package Configurations
<div class="image_block">
  <img src="/wp-content/uploads/users/onpnt/ssis_delete_4.gif" alt="" title="" width="211" height="120" />
</div>

 10. Check Enable package configurations
 11. Click Add and enter C:DelConfig.xml to the specify configuration settings directly textbox
<div class="image_block">
  <img src="/wp-content/uploads/users/onpnt/ssis_delete_5.gif" alt="" title="" width="526" height="341" />
</div>

 12. Click Finish and Close to the configurations editor. 
 13. You can open the XML file to edit the folder location and the log file location now
<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/ssis_delete2_0.gif" alt="" title="" width="819" height="727" />
</div>

 14. Import the package into SSIS and execute it to run. 

## Method two: Foreach Loop Container

  1. Bring over a Foreach Loop Container in the Control Flow
  2. Create these variables
<div class="image_block">
  <img src="/wp-content/uploads/users/onpnt/ssis_delete_6.gif" alt="" title="" width="273" height="129" />
</div>

  3. Bring over a Script Task and drop it into the Foreach Loop Container
<div class="image_block">
  <img src="/wp-content/uploads/users/onpnt/ssis_delete_7.gif" alt="" title="" width="301" height="329" />
</div>

  4. Double click the Script Task and add these ReadOnly variables
<div class="image_block">
  <img src="/wp-content/uploads/users/onpnt/ssis_delete_8.gif" alt="" title="" width="628" height="378" />
</div>

  5. Click the Edit Script button and add this code
```csharp
using System;
using System.Data;
using System.IO;
using Microsoft.SqlServer.Dts.Runtime;
using System.Windows.Forms;

namespace ST_216af79b563f4866bb64f6043b232b4e.csproj
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
            try
            {
                FileInfo fi = new FileInfo(Dts.Variables["User::filefocus"].Value.ToString());

                if (fi.LastWriteTime < (DateTime)Dts.Variables["User::daysback"].Value)
                {
                    Dts.TaskResult = (int)ScriptResults.Success;
                }
            }
            catch (Exception Ex)
            {
                Dts.Events.FireError(1, "", "BOOM!!!  " + Ex.Message.ToString(), "", 0);
            }
        }
    }
}
```

  6. Close the code editor and hit OK to exit and save the script task settings.
  7. Bring over and drop a File System Task into the Foreach Loop Container
  8. Connect the Script Task to the File System Task
<div class="image_block">
  <img src="/wp-content/uploads/users/onpnt/ssis_delete_9.gif" alt="" title="" width="284" height="267" />
</div>

  9. Double click the Foreach Loop Container and go to Collection.
 10. Enter in a default folder
<div class="image_block">
  <img src="/wp-content/uploads/users/onpnt/ssis_delete_10.gif" alt="" title="" width="469" height="348" />
</div>

 11. In Variable Mappings, drop down the Variable and select the User::filefocus variable and leave the index at 0
<div class="image_block">
  <img src="/wp-content/uploads/users/onpnt/ssis_delete_11.gif" alt="" title="" width="533" height="104" />
</div>

 12. Click OK to close the editor
 13. Double click the File System Task
 14. Change Operation to Delete
 15. Change IsSourcePathVariable to True
 16. SourceVariable to User::filefocus
<div class="image_block">
  <img src="/wp-content/uploads/users/onpnt/ssis_delete_12.gif" alt="" title="" width="449" height="195" />
</div>

 17. Click OK to exit and save
 18. Add a package configurations file as an XML file
 19. Right click an empty space in the Control Flow window
<div class="image_block">
  <img src="/wp-content/uploads/users/onpnt/ssis_delete_13.gif" alt="" title="" width="413" height="135" />
</div>

<div class="image_block">
  <img src="/wp-content/uploads/users/onpnt/ssis_delete_14.gif" alt="" title="" width="469" height="400" />
</div>

 20. Enter FileEnumConfig.xml and click, Next
 21. Click the variables filefocus and folder
<div class="image_block">
  <img src="/wp-content/uploads/users/onpnt/ssis_delete_15.gif" alt="" title="" width="402" height="205" />
</div>

 22. Name the configuration 
<div class="image_block">
  <img src="/wp-content/uploads/users/onpnt/ssis_delete_16.gif" alt="" title="" width="419" height="372" />
</div>

 23. Click Finish and then Close to exit and save
 24. Click SSIS in the menu strip and select Logging
<div class="image_block">
  <img src="/wp-content/uploads/users/onpnt/ssis_delete_17.gif" alt="" title="" width="254" height="187" />
</div>

 25. Check container FileEnumTest
<div class="image_block">
  <img src="/wp-content/uploads/users/onpnt/ssis_delete_18.gif" alt="" title="" width="356" height="106" />
</div>

 26. Select SSIS log provider for Text Files
<div class="image_block">
  <img src="/wp-content/uploads/users/onpnt/ssis_delete_19.gif" alt="" title="" width="476" height="106" />
</div>

 27. Drop down configuration and select New Connection
 28. Select Create File and enter C:DelbyFilEnum.txt
<div class="image_block">
  <img src="/wp-content/uploads/users/onpnt/ssis_delete_20.gif" alt="" title="" width="476" height="241" />
</div>

 29. Click OK and then OK to exit and save
 30. You can open the XML file to edit the folder location and the log file location now
 31. Import the package into SSIS and execute it to run. 

Logging from the first method appears in blocks like this

> File C:TestDeleteSystemIOFile_20100603121501.trn Deleted on 6/4/2010 7:11:38 AM
  
> File C:TestDeleteSystemIOFile_20100603123001.trn Deleted on 6/4/2010 7:11:38 AM 

compared to the SSIS logging method of

> Diagnostic,onpnt,onpnt,Delete Old File,{E4830550-A51A-46F3-A374-243888315707},{969D771F-24A9-4A57-B98D-42B025BE2573},6/4/2010 7:31:25 AM,6/4/2010 7:31:25 AM,0,(null),Trying to delete file 'C:TestDeleteFileSystemTaskFile_20100603191001.trn'.
  
> Diagnostic,onpnt,onpnt,Delete Old File,{E4830550-A51A-46F3-A374-243888315707},{969D771F-24A9-4A57-B98D-42B025BE2573},6/4/2010 7:31:25 AM,6/4/2010 7:31:25 AM,0,(null),Finished deleting file 'C:TestDeleteFileSystemTaskFile_20100603191001.trn'.

## Conclusions

The use of the script task and going to the System.IO namespace to do the work has some benefits over the second method. The largest was speed. The second was the administrative and development time needed. 

Both of these methods are sound and good options. The File System Task usage removes the need for more extensive programming over the Script Task. There are also benefits in the logging method used and the amount of information we can easily obtain during the execution progress. Picking either method will be decided on comfort level with coding in .NET and performance requirements while giving up or gaining functionality.