---
title: Import directory of XML files into SQL Server 2005
author: Ted Krueger (onpnt)
type: post
date: 2009-04-27T00:41:27+00:00
ID: 398
url: /index.php/datamgmt/datadesign/import-directory-of-xml-files-into-sql-s-2005/
views:
  - 11926
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
Here is an interesting way to import a directory that contains XML files using SQLCLR and T-SQL. Realistically this type of process will be placed into an executable or windows service given your typical requirements. SQL Server probably should be an option, but more than likely a secondary option to windows development. In some cases like one I had a year or so ago, I did have to do this and thought I would share.
  
SQLCLR comes in for two tasks. The first is to grab some file attributes and the second is to move files around. The file attribute procedure will receive 3 parameters, path to an archive directory, root path to grab files from and the work table to initially insert the data. We use a work table for the simple task of ensuring we do not get files into the main table multiple times. 

Here is the CREATE statements for the two tables I’ll use in this example

sql
CREATE TABLE [dbo].[XMLTestArchive](
	[filenm] [varchar](500) NULL,
	[timecreated] [datetime] NULL,
	[rownum] [int] NULL,
	[xmldata] [xml] NULL
) ON [PRIMARY]
GO
```
sql
CREATE TABLE [dbo].[XMLImportWT](
	[filenm] [varchar](500) NULL,
	[timecreated] [datetime] NULL,
	[rownum] [int] NULL,
	[xmldata] [xml] NULL
) ON [PRIMARY]
GO
```
Basically they are identical but one I have plans to ship offsite for recovery purposes. The path parameter is important for archiving the files. This way they go to normal tape backups and can be restored with the slower process of file backup services. The basic task is to ensure a structure exists of, “[archive directory]YYYYMM”. All the files will be moved accordingly to these folders once they are imported into our table and any other needed processing performed. You can manage that directory structure simply by doing …

```CSHARP
public static void ImportFileAttributes(string dest_path,string source_path, string insert_Table)
{
//check the year archive directory
if (!(Directory.Exists(path + System.DateTime.Now.Year.ToString())))
 {
 Directory.CreateDirectory(path + System.DateTime.Now.Year.ToString());
 }
//check the month
if (!(Directory.Exists(path + System.DateTime.Now.Year.ToString() +  System.DateTime.Now.Month.ToString())))
 {
 Directory.CreateDirectory(path + System.DateTime.Now.Year.ToString() + "\"   + System.DateTime.Now.Month.ToString());
 }
```
Once we do this we know we have our directory structure. Sense this process will run under a scheduled SQL Agent job we know the SQL service credentials have access to the local folder structure. If you decide to move this to a share on the network, make sure that you have read/write to the share. Please secure these types of processes. Everyone knows my push for security and not turning your head simply when things are easier to not set them up securely. More error handling should be added also over these examples. You can use my code but make sure you actually test it under pressure and scenarios that can affect true production systems. Remember, just because I wrote it doesn&#8217;t mean it is necessarily the best way to do something 🙂

Next step is to enumerate the XML files in the directory. That again is a pretty simple task and can be done in many methods. I used the Direcotory.GetFiles and then iterate through by grabbing each files FileInfo in order to get the attributes we want.

```CSHARP
string archivepath = path + System.DateTime.Now.Year.ToString() + "\" + System.DateTime.Now.Month.ToString();

DataTable workTable = new DataTable("fileinfo");
workTable.Columns.Add("filename");
workTable.Columns.Add("timecreate");
DataRow row;
string[] fileEntries = Directory.GetFiles(etl_path , "*.xml");

foreach (string fileName in fileEntries)
  {
  row = workTable.NewRow();
  FileInfo fileInfo = new FileInfo(fileName);

  row["filename"] = fileName;
  row["timecreate"] = fileInfo.CreationTime;
  workTable.Rows.Add(row);
  }
```
Now the best way we can dump our data into the work table in SQL Server is by using the SqlBulkCopy method. 

```CSHARP
SqlConnection cn = new SqlConnection("Data Source=(local);Initial Catalog=DBA05;Integrated Security=SSPI;");
cn.Open();
using (SqlBulkCopy s = new SqlBulkCopy(cn))
 {
 try
  {
   s.DestinationTableName = insert_temp;
   s.BatchSize = 10000;
   s.SqlRowsCopied += new SqlRowsCopiedEventHandler(s_SqlRowsCopied);
   s.WriteToServer(workTable);
   s.Close();
  }
 catch (SqlException e)
  {
   throw e;
  }
 finally
  {
   cn.Close();
  }
 }
static void s_SqlRowsCopied(object sender, SqlRowsCopiedEventArgs e)
{
 SqlContext.Pipe.Send("Copied " + e.RowsCopied.ToString());
}
```
Once your SQL Server Project is all setup and your procedure is formed, deploy it to a test instance that has CLR enabled. If you need to enable CLR and have not done so yet, I highly recommend taking the next steps.

First, SQLSkills.com has a great whitepaper by Kimberly Tripp
  
http://www.sqlskills.com/resources/Whitepapers/SQL%20Server%20DBA%20Guide%20to%20SQLCLR.htm
  
This is a great overview of SQL CLR and a must read.

Second, read Adam Machanic blog from a few years ago title “Writing SQL Server CLR routines”.
  
http://searchsqlserver.techtarget.com/tip/0,289483,sid87_gci1165410,00.html

Now back to the process at hand…

For test I’m using local directories, C:XML and C:XML_ARCHIVE.
  
Given the procedure we just created was deployed successfully, I can now call the procedure to process everything in C:XML as

sql
Exec dbo.ImportFileAttributes 'C:XML_ARCHIVE','C:XML','[XMLImportWT]'
```
Executing this I can now see in XMLImportWT that I’ve grabbed the file names and file creation date and times.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//xmlimport_1.gif" alt="" title="" width="540" height="141" />
</div>

Now let’s finish the process and grab the XML data and move everything from the work table to the table that we actually want in our DR strategies.
  
First step, I will update the rownum column

sql
Update a
Set rownum = rowid
from [XMLImportWT] a
join (select ROW_NUMBER() OVER (ORDER BY filenm ASC) AS ROWID, * from [XMLImportWT]) b on a.filenm = b.filenm
```
This only gives me looping structure and the column really isn’t required but one thing it does, is tell me what was batched together and inserted given any execution. 

Next I want to grab all the XML data and get it into the XML type column “xmldata”
  
Create my supporting variables in order to process everything along with the ability to utilize an existing SQL CLR procedure I’ve used to move files around. That procedure is nothing more than a File.Move method call.

The main process to grab the XML data and update the work table is below

```CSHARP
declare @filename varchar(255);
declare @loop int
declare @int int
declare @month varchar(2)
declare @year varchar(4)
declare @dest varchar(1000)
declare @filename_short varchar(100)
set @month = Month(Getdate())
set @year = Year(Getdate())
set @dest = @dest + @year + '' + @month + ''
set @loop = (select count(*) from [XMLImportWT])
set @int = 1

While @int <= @loop
 begin
   set @filename = (select filenm from [XMLImportWT] where rownum = @int);
   declare @SQLString nvarchar(max);
   set @SQLString = N'UPDATE [XMLImportWT] Set xmldata = (SELECT * FROM OPENROWSET(BULK ''' + @filename + ''', SINGLE_BLOB) AS x) WHERE rownum = ' + Cast(@int as varchar(10));

   exec sp_executesql @SQLString;
   set @filename_short = @dest + Reverse(Left(Reverse(@filename),Charindex('',Reverse(@filename))-1))

   exec dbo.MoveFile @filename, @filename_short
   set @int = @int + 1
 end
```
Now it is just a matter of inserting the work table contents into the primary table. This is where we can filter out any file that is found in the primary table that may have been duplciated in the directory or some other process caused the duplication to come up in the process.

sql
insert into XMLTestArchive
select * from [XMLImportWT]
where filenm not in (select filenm from XMLTestArchive)
```
After running all of this, the XML file should have been moved to the yyyymm folder as shown below

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//xmlimport_2.gif" alt="" title="" width="443" height="160" />
</div>

The XMLTestArchive table also has all of our XML and file attribute data

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//xmlimport_3.gif" alt="" title="" width="628" height="123" />
</div>

This seems to run pretty well. I’d like to hear performance improvements on my T-SQL if anyone has some of course. I wrote this without looking at my original coding but I think it was pretty close minus all the extra steps to handle errors and such. 

Wrapping this all up in a procedure that we can call would look like this

sql
CREATE PROCEDURE [dbo].[GetAllXMLFiles] (@dest varchar(255),@source varchar(255),@workTable sysname)
As
Set nocount On
--truncate temp work table to remove old imports
truncate table [XMLImportWT]

--call SQLCLR procedure to grab file attributes (time created and fully qualified file name
Exec dbo.ImportFileAttributes @dest,@source,@workTable;

--start the work process of importing the XML data and moving the files to an archive directory
--update work table 
Update a
Set rownum = rowid
from [XMLImportWT] a
join (select ROW_NUMBER() OVER (ORDER BY filenm ASC) AS ROWID, * from [XMLImportWT]) b on a.filenm = b.filenm

declare @filename varchar(255);
declare @loop int
declare @int int
declare @month varchar(2)
declare @year varchar(4)
declare @filename_short varchar(100)
set @month = Month(Getdate())
set @year = Year(Getdate())
set @dest = @dest + @year + '' + @month + ''
set @loop = (select count(*) from [XMLImportWT])
set @int = 1

While @int <= @loop
 begin
   set @filename = (select filenm from [XMLImportWT] where rownum = @int);
   declare @SQLString nvarchar(max);
   set @SQLString = N'UPDATE [XMLImportWT] Set xmldata = (SELECT * FROM OPENROWSET(BULK ''' + @filename + ''', SINGLE_BLOB) AS x) WHERE rownum = ' + Cast(@int as varchar(10));

   exec sp_executesql @SQLString;
   set @filename_short = @dest + Reverse(Left(Reverse(@filename),Charindex('',Reverse(@filename))-1))

   exec dbo.MoveFile @filename, @filename_short
   set @int = @int + 1
 end

insert into XMLTestArchive
select * from [XMLImportWT]
where filenm not in (select filenm from XMLTestArchive)

Set nocount off
```
And the call would then just be

sql
Exec [GetAllXMLFiles] 'C:XML_ARCHIVE','C:XML','[XMLImportWT]';
```
This handles the entire process for us and can be placed in a SQL Agent job for automating the process.

I went over a few different topics by doing all of this. XML file import, SQLCLR moving files, SQLCLR FileInfo and getting file attributes and some simple directory navigation and creation. The entire task can be done using xp_cmdshell and T-SQL and I wouldn’t put that option aside. This was one solution over that method. I mentioned DR a few times in this and the reason for that was due to how I used something very similar in a production envirinment. The reason I did this was due to orders coming in as XML files and I needed a good way to grab these files as quick as possible and get them into a DR strategy. My best option was to utilize the already existing log shipping methods. Once I had the XML data, file creation datetime and file name in a table, it was trivial to get the data offsite. It overcomes longer restores from tape as well.

Given a situation the XML can be redirected to the database server directly, SQL Server Broker would be the option to look into. Either that or web services using endpoints. There are plenty of options in that situation.