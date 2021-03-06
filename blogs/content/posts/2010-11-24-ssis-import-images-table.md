---
title: Using SSIS to import a directory of images
author: Ted Krueger (onpnt)
type: post
date: 2010-11-24T18:02:45+00:00
ID: 958
excerpt: 'SQL Server provides database professionals with several paths in order to import and export data in and out of many data sources and file services.  One main task, the Foreach Loop Container, allows for processing large (or small) groups of files with one contained and manageable process.  Tasks that once were difficult and required larger scale development efforts have now moved into the realm of the rapid development methodology.  Rapid Development is defined as requiring a much lower amount of resources in order to push a process into a production environment.  Resources can contain any one of the main components in developing strategies such as management, developers, infrastructure changes and so on.  The resources take time in planning stages, developing stages and testing stages.  Accomplishing this methodology is done by placing each of these on top of each other in a sense.  Planning is done alongside developing and testing.'
url: /index.php/datamgmt/dbprogramming/ssis-import-images-table/
views:
  - 13680
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - import image
  - rapid development
  - sql server 2008
  - sql server integration services
  - ssis

---
SQL Server provides database professionals with several paths in order to import and export data in and out of many data sources and file services. One main task, the Foreach Loop Container, allows for processing large (or small) groups of files with one contained and manageable process. Tasks that once were difficult and required larger scale development efforts have now moved into the realm of the rapid development methodology. Rapid Development is defined as requiring a much lower amount of resources in order to push a process into a production environment. Resources can contain any one of the main components in developing strategies such as management, developers, infrastructure changes and so on. The resources take time in planning stages, developing stages and testing stages. Accomplishing this methodology is done by placing each of these on top of each other in a sense. Planning is done alongside developing and testing. 

SSIS promotes this in a way just by the imagery that the designer, BIDS (Business Intelligence Studio), uses while you develop packages. From most SSIS packages, you can compile a flow chart in great detail. Jumping ahead on the topic and task at hand; take the example below of the finished package for importing a directory of images into a table in a SQL Server database.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/ssis_image_01.gif" alt="" title="" width="328" height="311" />
</div>

With the steps shown we can write a flowchart as shown below

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/ssis_image_02.gif" alt="" title="" width="394" height="371" />
</div>

The process in both representations is readable in the same manner. Process starts, then checks and if required, creates an archive directory and then processes the images internally in a subprocess (container). This added benefit of the way BIDS and the visual aspects of the development studio helps promote rapid development and quick documentation. All of this means lower to mid-level complex processing initiatives can be developed faster.

Remember, with any process, the complexity and depth of the requirements are considered if the longer planning process can be performed while developing them. Planning out a process before hand when a certain point is passed, should be performed. Combining the planning, development and testing at one time can cause the initiative to be longer if misused.

Back to the process at hand&#8230;

The outlined flow and package above has the goal of iterating through a folder and inserting the images found in it into a SQL Server database. Some of the dynamic needs in this are:

  1. Passing the type of images to insert
  2. Passing the directory to look in
  3. All connections to SQL Server

For error handling, each insert will be allowed to error only those insert statement. This means the package should not exit with an error state simply because one image failed to insert. Event handlers can be used to accomplish this.
  
The following statements will create the supporting database and tables. Setting up the main table to insert into and the primary error handling table initially will help speed the process of developing our package up.

```tsql
CREATE DATABASE [IMAGES] ON  PRIMARY 
( NAME = N'IMAGES', FILENAME = N'C:Program FilesMicrosoft SQL ServerMSSQL10_50.XPS2008R2MSSQLDATAIMAGES.mdf' , SIZE = 14336KB , MAXSIZE = UNLIMITED, FILEGROWTH = 1024KB )
 LOG ON 
( NAME = N'IMAGES_log', FILENAME = N'C:Program FilesMicrosoft SQL ServerMSSQL10_50.XPS2008R2MSSQLDATAIMAGES_log.ldf' , SIZE = 1792KB , MAXSIZE = 2048GB , FILEGROWTH = 10%)
GO
CREATE TABLE WebImages 
( 
    ImageName varchar(50) not NULL PRIMARY KEY, 
    ImageSource varbinary(max) not null 
)
CREATE TABLE WebImageImportErrorLog
( 
    ImageName varchar(50),
    ErrorDesc VARCHAR(2000),
    PackageID VARCHAR(255),
    ExeDateTime VARCHAR(50),
    UserExeName VARCHAR(255)
)```
To insert an image into the table WebImages, use OPENROWSET with SINGLE_BLOB. This method is shown as

```tsql
INSERT INTO WebImages (ImageName, ImageSource) 
SELECT 
	'C:UsersonpntDocumentsImages 001.jpg' ImageName
	,BulkColumn
FROM 
OPENROWSET (BULK 'C:UsersonpntDocumentsImages 001.jpg', SINGLE_BLOB) AS Images ```
Notice that we set the ImageName to a primary key. This is due to the table following the same constraints as directory services. No file may exist in the same location with the same name. We propagate this concept to the table.

**Create the package &#8211; Preparation**

Variables are a key to making this process reusable and portable. For this example, four variables will be utilized.

  * SqlInsertCmd: Used to build the INSERT statement based on the image currently being processed
  * ArchiveDirectory: Mentioned earlier to create the archive folder once images are processed
  * ImageFileFocus: The place holder of the image being processed
  
    SourceDirectory: Directory to work on that is holding the images

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/ssis_image_03.gif" alt="" title="" width="331" height="151" />
</div>

Data types for these variables are all strings. The scope will primarily be set to the package level with a few set to the container level.
  
Create the variables as shown above after moving the following into the control flow window.

File System Task, Foreach Loop Container with an Execute SQL Task and File System
  
Task within the container

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/ssis_image_04.gif" alt="" title="" width="148" height="211" />
</div>

**Archive Task**

The first task that needs to be accomplished is the archive directory. In order to prevent unwanted errors to be logged be reprocessing images on each execution of the package, we want to move the images to a folder that is designated as processed already. This can be accomplished with a File System Task and the create directory operation.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/ssis_image_05.gif" alt="" title="" width="442" height="170" />
</div>

Set the UseDirectoryIfExists to True so if the directory exists already, we do not unintentionally remove or overwrite the directory. This may cause unwanted loss of images that exist in the directory. The Archive directory for this example is set in the directory that is being processed so using the source directory while adding the “Archive” additional path is sufficient. This is done in the ArchiveDirectory variable as an expression

@[User::SourceDirectory] + &#8220;\Archive&#8221;

**The container**

The Foreach Loop Container is truly the core processing mechanism in this package. With the Foreach Loop Container, iterating all files in a specified directory is made simple in a sense. It is simple due to the limited requirement of properties to get the container looping through the files.

The key properties that are required to set the container are directory (Folder), file extensions, resulting return of string that will be the file currently focused on and a variable mapping. The variable mapping is used to set a variable to the file that is currently being processed by the container so it can be passed to other tasks within the container.

To set the properties for the container to process the directory from the variable SourceDirectory, use expressions. This will set the property at runtime. This allows us to quickly change where the processing will occur if the folder is changing often, the moved or the package is going to be used for other tasks. Set the expression by going to the Collection window in the Foreach Loop Editor, click the ellipse to open the Property Expression Editor and select Directory for Property and set the Expression to the variable, @[User::SourceDirectory]. (shown below)

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/ssis_image_06.gif" alt="" title="" width="482" height="169" />
</div>

Use a hardcoded folder value for the setup and enter in “.jpg” (or the image types you want to import) in the Files text box.

**Execute SQL Task – INSERT**

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/ssis_image_07.gif" alt="" title="" width="628" height="272" />
</div>

**Error Handling**

Event Handlers are used to catch any issues that may arise when inserting the image into SQL Server. The most common error that will come up with the process is a primary key violation. This is due to the primary key being the file name (or image) in the table WebImages. In the case an image was placed in the folder again or the archive process failed, there is a chance for the image to be inserted again. This is not ideal in this processing and would be set as a secondary processing design to update the table. That design would be set with either child package or a variable setting to validate the tasks to begin processing in the package itself. (for a later discussion)
  
To setup the event handler:
  
While the Execute SQL Task is highlighted, click the Event Handlers tab. Click the link to create the OnError event handler for the SQL task. Bring an Execute SQL Task over into the window.

The connection for the task will be to the IMAGES database created earlier, and an insert into the error table also created earlier. The system level information and image file name will be inserted into the error table so they can be analyzed later. To insert the system variables, use the insert statement shown below

```tsql
INSERT INTO WebImageImportErrorLog 
([ImageName]
      ,[ErrorDesc]
      ,[PackageID]
      ,[ExeDateTime]
      ,[UserExeName])
VALUES (?,?,?,?,?)```<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/ssis_image_08.gif" alt="" title="" width="628" height="363" />
</div></p> 

Set ByPassPrepare to True so the statement is not parsed.
  
Move to the to the Parameter Mapping section. Each ? will map based on the ordinal positions mapped. This is set by the Parameter Name as shown below. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/ssis_image_09.gif" alt="" title="" width="628" height="137" />
</div></p> 

The last property to set is a system variable in order to manage the errors when they occur. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/ssis_image_10.gif" alt="" title="" width="628" height="117" />
</div>

Set the Propagate system variable to False so the errors do not flow back up to the container and stop the processing of the next image.

**Archive the processed image**

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/ssis_image_11.gif" alt="" title="" width="628" height="261" />
</div>

The File System Task to perform the archive task will utilize the Move file type. Using the ImageFileFocus variable for the source, the last setting required is the archive directory path. The file name is not required on the move.
  
Set the DelayValidation to true or the variable setting will cause it to error given it is empty until the package is executed.

**Processing**

Putting this all together we arrive at the package outlined in the beginning of this discussion. Executing this package initially and then a second time will show the normal process of inserting all images into the WebImages table along with the error handling and logging put in place.

Execute the package initially

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/ssis_image_12.gif" alt="" title="" width="104" height="146" />
</div></p> 

Once complete, validate the WebImages table by selecting the contents 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/ssis_image_13.gif" alt="" title="" width="516" height="137" />
</div></p> 

To test the event handler error logging, move the images back into the root directory from the archive directory and execute the package a second time. While processing, the Execute SQL Task will start to throw errors based on the primary key violation. This can be seen by selecting from the WebImageErrorLog table

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/ssis_image_14.gif" alt="" title="" width="628" height="140" />
</div>

**Closing**

SSIS and the Foreach Loop Container allow for quick setup and processing of entire directory contents. Within the container, the use of T-SQL, Data Flow Tasks and other processing can be used to fully validate, transform and import data while iterating through those files. This promotes rapid development methods and allows for shorter and less costly initiatives to transport from development to testing and into production.