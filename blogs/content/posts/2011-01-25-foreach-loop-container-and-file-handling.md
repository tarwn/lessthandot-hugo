---
title: Backup file contents with SSIS – Foreach Loop Container and file handling
author: Ted Krueger (onpnt)
type: post
date: 2011-01-25T10:14:00+00:00
excerpt: 'In the first part of building the package that will look at the complete contents and back up the files found in a directory and subdirectories, we discussed the designing phase of building the package itself.  In this part we will take that design and start the development process.'
url: /index.php/datamgmt/datadesign/foreach-loop-container-and-file-handling/
views:
  - 16600
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Microsoft SQL Server
  - SSIS

---
In the [first part of building the package][1] that will look at the complete contents and back up the files found in a directory and subdirectories, we discussed the designing phase of building the package itself. In this part we will take that design and start the development process. 

For the first step in the flow chart we defined, there is the starting process which first executes the actual work of looping through the directory and finding the files that we request. This then inserts the contents and some information about them into a staging area. To refine this, our looping process will be a Foreach Loop Container, our file information collection will be a Script Task and the insert process into the staging area will be an Execute SQL Task. 

<div class="image_block">
  <a href="/wp-content/uploads/blogs/All/.png?mtime=1295748012"><img alt="" src="/wp-content/uploads/blogs/All/.png?mtime=1295748012" width="624" height="325" /></a>
</div>

**The Foreach Loop Container**

A Foreach Loop Container has the ability to look into a master object (Enumerator Type) and focus on each child object found within it. In SQL Server 2008, the list of enumerator types has grown to include types such as ADO.NET, Nodelist, SMO and more which can be found [here][2]. The enumerator we will focus on is the File Enumerator. The File Enumerator takes two parameters and a few option settings that we require for our processing. Configuration files, or .Config files, are the files we want to extract and insert into our staging area. The directory should be dynamic by being held in a variable and then either set with configurations or directly in the variable itself. This allows us to create a [dynamic nature to the package][3]. 

Steps to creating out Foreach Loop Container 

Within BIDS and with the Control Flow window open, bring over a Foreach Loop Container into the Control Flow area. Before going into the editor for the container, create two variables. One named ScanFolder and another named IndexFile. Make both of these variables data types, String. Next, open the editor for the container and go directly to the Collection section.

In the Collection section, the options Folder and Files are required for our needs. The requirements are to pass the folder in as a dynamic value from our variables that are set prior to the containers execution. To do this, the property for Directory needs to be set to the variable ScanFolder. Setting the properties for the container can be done through the expressions window. Open the property expressions editor by clicking the button next to the Expressions option (as shown below)

<div class="image_block">
  <a href="/wp-content/uploads/blogs/All/-1.png?mtime=1295748013"><img alt="" src="/wp-content/uploads/blogs/All/-1.png?mtime=1295748013" width="860" height="207" /></a>
</div>

In the Property Expressions Editor, select the property, Directory and then enter the expression, @[User::ScanFolder]. This is referencing the variable created earlier. Alternatively, the Expression Builder can be used to build the expressions.

In the Files option enter *.config. This will only perform operations defined in the container on files that contain the pattern .config. In this case we are looking for configuration files which have the .config extension.

Ensure the Traverse subfolders option is checked. This will enumerate the directory specified and then all of the subdirectories found within the directory.

The last option that is needed to complete the setup of the container is the mapping of the objects the enumerator finds. This mapping will set our variable IndexFile for each file found. In the Variable Mappings window, click the dropdown for the Variable and select the User::IndexFile. Leave the default 0 for the index mapping. If multiple variables were to be set in this area, increase or manipulate the index to the variables you want to set.

**Script Task**

After exiting the loop container, bring over a Script Task and an Execute SQL Task into the Foreach Loop Container. Create another variable at this time named, FileModDate and make the data type DateTime. In order to determine if a file has been changed and later decide if an update or insert operation is required, the files last modified date and time is needed. This is accomplished through the Script Task and the System.IO assembly. Open the Script Task Editor and first add the variables we need into the Read and Write Variable boxes. 

  * ReadOnlyVariables = User::IndexFile
  * ReadWriteVariables = User::FileModDate

Open the script editor by clicking the Edit Script button in the same window

The coding needed to acquire the last modified date and time is as follows. Enter this code into the Main() method

<pre>if (File.Exists(Dts.Variables["IndexFile"].Value.ToString()))
            {
                Dts.Variables["FileModDate"].Value = Convert.ToDateTime(File.GetLastWriteTime(Dts.Variables["IndexFile"].Value.ToString()));
                Dts.TaskResult = (int)ScriptResults.Success;
            }
            else
            {
                Dts.TaskResult = (int)ScriptResults.Failure;
            }</pre>

Close the script editor (which in turn, saves the code) and click OK to close and save the changes to the Script Task. Using the success precedence line (green connector), connect the Script Task to the Execute SQL Task. 

**Execute SQL Task**

To insert the file contents of the files the loop container finds, the OPENROWSET method with an INSERT statement is used. This method is efficient and allows us to insert configuration files that may not be well formed XML data types. If XML contents were the only objective, the OPENROWSET method can be used while converting the BulkColumn into an XML data type and inserting it into an adjoining XML column in the staging table. A Data Flow task could also be used in this step for mapping directly to the destination of the staging table. 

Create the following staging table that will be used before editing the Execute SQL Task.

<pre>CREATE TABLE [dbo].[ConfigRepository_Temp](
	[ContentConfigID] [int] IDENTITY(1,1) NOT NULL,
	[SystemConfigID] [int] NULL,
	[ModifiedDate] [datetime] NULL,
	[ConfigPath] [varchar](1500) NULL,
	[ImportBy] [varchar](255) NULL,
	[FileContents] [varchar](max) NULL
) ON [PRIMARY]

GO</pre>

This table will act as the reusable holding table for all the files and information we find during the enumeration of the directories. 

Open the Execute SQL Task Editor and if not already defined, add a new connection to the Connection option. Set the BypassPrepare option to False. The BypassPrepare option will be needed so the SQL statement we use can be built based on the contents and file that is currently focused on. Go to the Expressions page and open the Property Expressions Editor. Select SqlStatementSource as the property and add the following into the Expression field. If space in the field is needed, open the expressions builder by clicking the button in the field.

<pre>"INSERT DBA.dbo.ConfigRepository_Temp (ModifiedDate,ConfigPath,FileContents,ImportBy)
SELECT '" + (DT_STR,24,1252)@[User::FileModDate]  + "','" + @[User::IndexFile]  + "',BulkColumn,SUSER_NAME() FROM OPENROWSET(Bulk '" + @[User::IndexFile]  + "', SINGLE_BLOB) [rowsetresults]"</pre>

Note the use of the FileModDate and IndexFile variables in this expression. The SUSER_NAME() function is also used to insert the user that is running the package. It is important to point out that later when this package is scheduled using the SQL Server Agent, the Agent Service Account will be placed into this field. That account also requires permissions to this table and other tables we create in this package.

Close all windows and click OK to complete the setup of the Execute SQL Task.

At this time the container should appear as below

<div class="image_block">
  <a href="/wp-content/uploads/blogs/All/-2.png?mtime=1295748013"><img alt="" src="/wp-content/uploads/blogs/All/-2.png?mtime=1295748013" width="351" height="246" /></a>
</div>

**Closing Part 2**

In this part of the series we learned how to setup a Foreach Loop Container and utilize variables to work with each object found in the enumeration of the type we chose. We spent time showing looking at the Script Task and the code that is part in finding the last modified date of a file as well as setting up a SQL Execute Task to dynamically build a statement using the variables that are set within the enumeration. 

In the part 3, the tables required for our finished design in the database will be created and the relationship between them along with the stored procedures to populate them while retaining the integrity between them will be done.

 [1]: /index.php/DataMgmt/ssis-1/ssis-defining-the-design-1
 [2]: http://msdn.microsoft.com/en-us/library/ms141724.aspx
 [3]: /index.php/DataMgmt/DBAdmin/variables-ssis-dynamic-parent-to-child