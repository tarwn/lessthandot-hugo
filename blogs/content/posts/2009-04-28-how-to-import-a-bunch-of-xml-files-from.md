---
title: How to import a bunch of XML files from a directory in T-SQL
author: SQLDenis
type: post
date: 2009-04-28T18:01:36+00:00
url: /index.php/datamgmt/datadesign/how-to-import-a-bunch-of-xml-files-from/
views:
  - 14508
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
tags:
  - import
  - sql server 2005
  - sql server 2008
  - xml

---
A couple of days ago onpnt posted the following blogpost: [Import directory of XML files into SQL Server 2005][1]
  
In that post he was using SQLCLR to import a bunch of files. Some of you might not be so familiar with .NET so I am providing a T-SQL way to do something similar

You will need to create a directory testxml on the c drive and put a bunch of XML files in there. If you don&#8217;t have any XML files then save the following two as file1.xml and file2.xml

file1.xml

<pre>&lt;MusicCollection&gt;
 &lt;Artist&gt;
  &lt;ArtistName&gt;Pink Floyd&lt;/ArtistName&gt;
 &lt;Album&gt;
  &lt;AlbumName&gt;Wish You Were Here&lt;/AlbumName&gt;
  &lt;YearReleased&gt;1975&lt;/YearReleased&gt;
  &lt;/Album&gt;
 &lt;Album&gt;
  &lt;AlbumName&gt;The Wall&lt;/AlbumName&gt;
  &lt;YearReleased&gt;1979&lt;/YearReleased&gt;
  &lt;/Album&gt;
  &lt;/Artist&gt;
 &lt;Artist&gt;
  &lt;ArtistName&gt;Prince&lt;/ArtistName&gt;
 &lt;Album&gt;
  &lt;AlbumName&gt;Purple Rain&lt;/AlbumName&gt;
  &lt;YearReleased&gt;1984&lt;/YearReleased&gt;
  &lt;/Album&gt;
 &lt;Album&gt;
  &lt;AlbumName&gt;Lotusflow3r&lt;/AlbumName&gt;
  &lt;YearReleased&gt;2009&lt;/YearReleased&gt;
  &lt;/Album&gt;
 &lt;Album&gt;
  &lt;AlbumName&gt;1999&lt;/AlbumName&gt;
  &lt;YearReleased&gt;1982&lt;/YearReleased&gt;
  &lt;/Album&gt;
  &lt;/Artist&gt;
 &lt;Artist&gt;
  &lt;ArtistName&gt;Incubus&lt;/ArtistName&gt;
 &lt;Album&gt;
  &lt;AlbumName&gt;Morning View&lt;/AlbumName&gt;
  &lt;YearReleased&gt;2001&lt;/YearReleased&gt;
  &lt;/Album&gt;
 &lt;Album&gt;
  &lt;AlbumName&gt;Light Grenades&lt;/AlbumName&gt;
  &lt;YearReleased&gt;2006&lt;/YearReleased&gt;
  &lt;/Album&gt;
  &lt;/Artist&gt;
  &lt;/MusicCollection&gt;</pre>

file2.xml

<pre>&lt;MusicCollection&gt;
 &lt;Artist&gt;
  &lt;ArtistName&gt;Pink Floyd&lt;/ArtistName&gt;
 &lt;Album&gt;
  &lt;AlbumName&gt;Wish You Were Here&lt;/AlbumName&gt;
  &lt;YearReleased&gt;1975&lt;/YearReleased&gt;
  &lt;/Album&gt;
 &lt;Album&gt;
  &lt;AlbumName&gt;The Wall&lt;/AlbumName&gt;
  &lt;YearReleased&gt;1979&lt;/YearReleased&gt;
  &lt;/Album&gt;
  &lt;/Artist&gt;
 &lt;Artist&gt;
  &lt;ArtistName&gt;Prince&lt;/ArtistName&gt;
 &lt;Album&gt;
  &lt;AlbumName&gt;Purple Rain&lt;/AlbumName&gt;
  &lt;YearReleased&gt;1984&lt;/YearReleased&gt;
  &lt;/Album&gt;
 &lt;Album&gt;
  &lt;AlbumName&gt;Lotusflow3r&lt;/AlbumName&gt;
  &lt;YearReleased&gt;2009&lt;/YearReleased&gt;
  &lt;/Album&gt;
 &lt;Album&gt;
  &lt;AlbumName&gt;1999&lt;/AlbumName&gt;
  &lt;YearReleased&gt;1982&lt;/YearReleased&gt;
  &lt;/Album&gt;
  &lt;/Artist&gt;
&lt;/MusicCollection&gt;</pre>

Now that we have our files we are ready to grab all the files in the directory. We will use a plain vanilla DOS dir command for this with the B switch so that we don&#8217;t get a lot of garbage returned. Here is what this block of code looks like

<pre>IF OBJECT_ID('tempdb..#tempList') IS NOT NULL
DROP TABLE #tempList

CREATE TABLE #tempList ([FileName] VARCHAR(500))

--plain vanilla dos dir command with /B switch (bare format)
INSERT INTO #tempList
EXEC MASTER..XP_CMDSHELL 'dir c:testxml /B'


--delete the null values
DELETE #tempList WHERE [FileName] IS NULL

-- Delete all the files that don't have xml extension
DELETE #tempList WHERE [FileName] NOT LIKE '%.xml'

--this will be used to loop over the table
alter table #tempList add id int identity
go</pre>

Now let&#8217;s see what has actually been inserted into the table

<pre>select * from #tempList</pre>

Output
  
&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;

<pre>FileName	id
file1.xml	1
file2.xml	2</pre>

The following table will be used to store the XML.

<pre>CREATE TABLE [dbo].[XMLImport](
    [filename] [VARCHAR](500) NULL,
    [timecreated] [DATETIME] NULL,
    [xmldata] [xml] NULL
) ON [PRIMARY]
GO</pre>

Here is where the import happens, since we have to use dynamic SQL to do the XML import it is better to use SP\_EXECUTESQL instead of EXEC since SP\_EXECUTESQL has output parameters.
  
I have put comments in this codeblock but if you need more information how exactly this works then leave me a comment.

<pre>truncate table XMLImport --in case you want to rerun just this codeblock
declare @Directory varchar(50)
select @Directory = 'c:testxml'

declare @FileExist int
DECLARE @FileName varchar(500),@DeleteCommand varchar(1000),@FullFileName varchar(500)

DECLARE @SQL NVARCHAR(1000),@xml xml

--This is so that we know how long the loop lasts
declare @LoopID int, @MaxID int
SELECT @LoopID = min(id),@MaxID = max(ID)
FROM #tempList



WHILE @LoopID &lt;= @MaxID
BEGIN

	SELECT @FileNAme = filename
	from #tempList
	where id = @LoopID

	SELECT @FullFileName = @Directory + @FileName 
	
	exec xp_fileexist @FullFileName , @FileExist output
	if @FileExist =1 --sanity check in case some evil person removed the file
	begin
	SELECT @SQL = N'select @xml = xml 
		FROM OPENROWSET(BULK ''' + @FullFileName +''' ,Single_BLOB) as TEMP(xml)'
	 
	-- Just like in the bedroom, this is where the magic happens
	-- We use the output functionality to fill the xml variable for later use
	EXEC SP_EXECUTESQL @SQL, N'@xml xml OUTPUT', @xml OUTPUT
	 
	
	--The actual insert happens here, as you can see we use the output value (@xml)
	INSERT XMLImport ([filename],timecreated,xmldata)
	SELECT @FileName,getdate(),@xml
	
	SET @DeleteCommand = 'del ' +  @Directory + @FileName 
	--maybe you want to delete or move the file to another directory
	-- ** here is how to delete the files you just imported
	-- uncomment line below to delete the file just inserted
	--EXEC MASTER..XP_CMDSHELL @DeleteCommand
	-- ** end of here is how to delete the files
	end

	--Get the next id, instead of +1 we grab the next value in case of skipped id values
	SELECT @LoopID = min(id)
	FROM #tempList
	where id &gt; @LoopID
END</pre>

So that is all the code that you need to make this happen, let&#8217;s see what is actually inserted into the table

<pre>select * from XMLImport</pre>

output
  
&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;

<pre>filename	timecreated		xmldata
file1.xml	2009-04-29 09:18:42.313	&lt;MusicCollection&gt;&lt;Artist&gt;&lt;ArtistName&gt;Pink Floyd....
file2.xml	2009-04-29 09:18:42.330	&lt;MusicCollection&gt;&lt;Artist&gt;&lt;ArtistName&gt;Pink Floyd....</pre>



### Attention/warning!!

Here are a couple of warnings for you. 

**xp_cmdshell**
  
It is not a best practice to have xp\_cmdshell enabled. As a matter of fact beginning with SQL Server 2005, the product ships with xp\_cmdshell disabled. If you try to run xp_cmdshell you will get the following message if it is not enabled
  
Server: Msg 15281, Level 16, State 1, Procedure xp_cmdshell, Line 1

SQL Server blocked access to procedure &#8216;sys.xp\_cmdshell&#8217; of component &#8216;xp\_cmdshell&#8217; because this component is turned off as part of the security configuration for this server. A system administrator can enable the use of &#8216;xp\_cmdshell&#8217; by using sp\_configure. For more information about enabling &#8216;xp_cmdshell&#8217;, see &#8220;Surface Area Configuration&#8221; in SQL Server Books Online.

To enable xp_cmdshell execute the following code

<pre>EXECUTE SP_CONFIGURE 'show advanced options', 1
RECONFIGURE WITH OVERRIDE
GO
 
EXECUTE SP_CONFIGURE 'xp_cmdshell', '1'
RECONFIGURE WITH OVERRIDE
GO
 
EXECUTE SP_CONFIGURE 'show advanced options', 0
RECONFIGURE WITH OVERRIDE
GO</pre>

**OPENROWSET** 
  
In SQL Server 2005 and 2008 OPENROWSET is also disabled by default, if you try to run an OPENROWSET query then you will see the following message:

Server: Msg 15281, Level 16, State 1, Line 1
  
SQL Server blocked access to STATEMENT &#8216;OpenRowset/OpenDatasource&#8217; of component &#8216;Ad Hoc Distributed Queries&#8217; because this component is turned off as part of the security configuration for this server. A system administrator can enable the use of &#8216;Ad Hoc Distributed Queries&#8217; by using sp_configure. For more information about enabling &#8216;Ad Hoc Distributed Queries&#8217;, see &#8220;Surface Area Configuration&#8221; in SQL Server Books Online.

To enable OPENROWSET and OPENQUERY you can use the previous script but instead of &#8216;xp_cmdshell&#8217; you will use &#8216;Ad Hoc Distributed Queries&#8217;. The script to enable Ad Hoc Distributed Queries is below

<pre>EXECUTE SP_CONFIGURE 'show advanced options', 1
RECONFIGURE WITH OVERRIDE
GO
 
EXECUTE SP_CONFIGURE 'Ad Hoc Distributed Queries', '1'
RECONFIGURE WITH OVERRIDE
GO
 
EXECUTE SP_CONFIGURE 'show advanced options', 0
RECONFIGURE WITH OVERRIDE
GO</pre>

**xp_fileexist**
  
The stored proc xp_fileexist is undocumented so be aware that it could change with a service pack or be removed all together



\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][2] forum or our [Microsoft SQL Server Admin][3] forum**<ins></ins>

 [1]: /index.php/DataMgmt/DataDesign/import-directory-of-xml-files-into-sql-s-2005
 [2]: http://forum.lessthandot.com/viewforum.php?f=17
 [3]: http://forum.lessthandot.com/viewforum.php?f=22