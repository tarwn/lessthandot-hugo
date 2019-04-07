---
title: Backup file contents with SSIS – INSERT/UPDATE Decisions
author: Ted Krueger (onpnt)
type: post
date: 2011-01-31T10:33:00+00:00
ID: 1021
excerpt: 'The last part of the series that shows how to create an SSIS Package to find and backup configuration files found on a server will go over the Data Flow Task (DFT).  The DFT part of this process is split as half of the total processing that will be done in the package.  The DFT will only be a small percentage of the overall processing time because of the Foreach Loop and staging area consuming the higher cost to the process.'
url: /index.php/datamgmt/dbadmin/backup-file-contents-with-ssis/
views:
  - 5357
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
The last part of the series that shows how to create an SSIS Package to find and backup configuration files found on a server will go over the Data Flow Task (DFT).  The DFT part of this process is split as half of the total processing that will be done in the package.  The DFT will only be a small percentage of the overall processing time because of the Foreach Loop and staging area consuming the higher cost to the process. 

 

There are a few critical steps that need to be handled as well as deciding if an update or insert is required.  In [part three][1] the stored procedures that handle the update and insert were shown.  These procedures handle managing the integrity of the tables while the main task of deciding which to execute is performed by tasks directly in the DFT.

 

**Review the Design**

 

Back in the [designing phase][2] the flowchart for the entire process that is being developed was shown.  The DFT portion was the secondary sub process to the start and enumeration processing.  This phase from the flowchart perspective is shown below

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-13.png?mtime=1296446146"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-13.png?mtime=1296446146" width="624" height="280" /></a>
</div>

 

In the entire process, the decision making tasks in this work of the package is where the most focus is spent.  The process could take advantage of the TRUNCATE or DELETE statements to clear the contents out of the tables.  Then after that, simply insert everything again.  That would cause the process to always run as if it was being run for the first time.  With testing, this process was averaging around 2.5 minutes to process 1100 files from a VM server.  This processing was the initial execution so all of the files were insert statements over the updates.  Upon another test of altering 50 files on the same server, the next run took 8 seconds on the DFT.  The contents of the files make these tests extremely hard to pinpoint to an actual solid average but the difference is undeniably large.  When running this process daily or even weekly, the shortest runtime is optimal in a production environment.   One way to rework this if the insert all method is wanted would be to handle the work entirely in the Foreach Loop.  The package as written does this insert into the staging table already so essentially, removing the DFT could gain performance.   

 

**Source to Lookup**

 

The Lookup Transformation is precisely what the term sounds like for meaning.  The transform attempts to make a match on each row from one set to the other.  Why is this important information to know about a Lookup?  The key is, “each row”.  For every row that is sent through the buffer the operation is performed.  This in terms of an extremely large volume of data means performance will severely suffer due to the row-by-row operations.  Now this should not make the Lookup Transformation something to stay away from.  It should however make the use of it critical to the fact; operations will be affected by how it behaves. 

There are options to the Lookup.  These consist of the Merge and a Script Transform.  Jamie Thomson is someone that I have learned a great deal from and his work takes up about 75% of my own bookmarked resources.  In fact, this very article being written is based off a process using the [Lookup and Conditional Split][3] seen here.  You will see that there is not much difference to take from this type of processing in SSIS.  Once you learn the steps, they move from your needs with slight changes very easily.

In that article Jamie has written, there is a link back to the MSDN forums in which a member took the time to [benchmark the performance][4] of the different methods to perform the task of a comparison look up from one source to another.  That resource can assist in making your own tests to determine your own strategy.  Never make an assumption that one way is more performance than the other until you’ve made your own proof of concept and performance analysis on your particular situation.

Getting back to our use of the Lookup; the goal of the Lookup in this situation is to check the existing data that has been imported on previous processing.  The output from this will result in either a matched row or not.  Based on that match, the row will either be inserted or sent to further check if the row should be updated.  As discussed previously, the value that will determine if the contents of a file are updated is based off the last time the file was modified.  It is a valid assumption that if the modified date has changed, the file in some way has been altered.  Even if the contents are exact, this processing overhead of updating contents that are not changed but only opened and saved, is far less than an equality check on the contents themselves.  

**Configuration of the Lookup**

Set the Lookup Transformation as follows:

  1. In the Lookup Transformation Editor 
      1. Set the “Specify how to handle rows with no matching entries” to “Redirect rows to error output”
      2. In Connection, select your connection manager and select the table dbo.SystemConfigMaster
      3. In Columns, map ConfigPath to ConfigPath and check ConfigID and CreateDate.
      4. Save the editor settings

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-14.png?mtime=1296446146"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-14.png?mtime=1296446146" width="209" height="182" /></a>
</div>

 

The next steps are to bring a Conditional Split into the Data Flow tab and also setup the redirected rows that did not match into an OLEDB Command to insert them as newly found files.

**Configuration of the Conditional Split**

  1. In the Conditional Split Transformation Editor, add the condition Row Needs Update.  The actual condition expression for this is, “CreateDate != ModifiedDate”
  2. Connect the Lookup to the Conditional Split with the success precedence connector based on output rows of a match being found.

No other changes are needed to the Conditional Split.  Click OK to save the condition and move to configuring the insert processing.

**Configuration to INSERT new rows**

  1. Add a Derived Column Transform and the failure precedence connector from the Lookup to it.
  2. In the Derived Column Transform Editor, create the base three columns be dragging them to the output area.  Additionally, drag the variables ServerName and FileNameOnly as columns.  Use type casts to retain the non-unicode values as shown below.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-15.png?mtime=1296446146"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-15.png?mtime=1296446146" width="472" height="233" /></a>
</div>

  1. Drag the success precedence to a new OLEDB Command.  
  2. In the OLEDB Command, set the connection manager to your connection.
  3. In the Component Properties tab, set the name to Insert new and make the SqlCommand, “dba_InsertNewConfig ?,?,?,?, ?” 
      1. This will map the columns in the row to the ordering index of the question marks which are set in the column mappings tab.
      2.  In the Column Mappings tab, set the mappings. 
          1. ModifiedDate_Source to @date
          2. ConfigPath_Source to @ConfigPath
          3. Contents_Srouce to @ConfigContents
          4. FileName_Source to @Filename
          5. ServerName_Source to @ServerName

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-16.png?mtime=1296446147"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-16.png?mtime=1296446147" width="387" height="257" /></a>
</div>

  1. Click OK to save the OLEDB Command

**UPDATE OLEDB Command**

The update command will be based on the same type of configuration as the insert command.  The procedure that was created in part 3 to perform an update will be used. 

  1. Drag the success precedence from the conditional split to a new OLEDB Command.  Set the output to Row Needs Update.
  2. In the OLEDB Command, set the connection manager to your connection.
  3. In the Component Properties tab, set the name to Update Rows and make the SqlCommand, “dba_UpdateNewConfig ?,?,?”
  4. In the Column Mappings tab, set the mappings. 
      1. ModifiedDate to @date
      2. ConfigID to @ConfigID
      3. XMLContents to @ConfigContents
      4. Click OK to save the OLEDB Command

A Row Sampling was used as the path for no updates.  This later was outputted to a destination for verification and testing.  This or a data viewer set is useful for monitoring during developing the package but ultimately is not part of the finished package.

**Execution**

At this point the package is ready to be executed on a machine.  For test purposes it is being executed locally.  The results are shown below.

The primary length for execution is done for building the staging table.  This direct import of the contents into a staging table, as discussed in part one, is done so we can optimize this initial step so the row-by-row operations are giving higher performance.  Total execution on the test machine with the discovery of 1027 *.config files was 2.6 minutes. 

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-17.png?mtime=1296446147"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-17.png?mtime=1296446147" width="344" height="385" /></a>
</div>

Querying these configuration tables shows the contents successfully inserted

 

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-18.png?mtime=1296446150"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-18.png?mtime=1296446150" width="624" height="266" /></a>
</div>

 

**Closing the Series**

Over this four part series a lot of things were discussed.  The Lookup, Conditional Split, OLEDB Command, variable usage and Foreach Loop Container all at a high level.  In the process, we setup a cool task for backing up of files on servers.  With SSIS and some of the components and tasks available, many things like this can be automated and provide a great recovery point in the event of a disaster at any level.

Part one &#8211; [Backup file contents with SSIS – Defining the Design][2]

Part two &#8211; [Backup file contents with SSIS – Foreach Loop Container and file handling][5]

Part three – [Backup file contents with SSIS – Supporting tables and structures][1]

 [1]: /index.php/DataMgmt/DBAdmin/backup-file-tables-procedures
 [2]: /index.php/DataMgmt/ssis-1/ssis-defining-the-design-1
 [3]: http://consultingblogs.emc.com/jamiethomson/archive/2006/09/12/ssis_3a00_-checking-if-a-row-exists-and-if-it-does_2c00_-has-it-changed.aspx
 [4]: http://social.msdn.microsoft.com/forums/en-US/sqlintegrationservices/thread/5afc3806-4681-4b6a-aed9-bdf56127ef49/
 [5]: /index.php/DataMgmt/ssis-1/foreach-loop-container-and-file-handling