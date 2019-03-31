---
title: Create XML Files Out Of SQL Server With SSIS And FOR XML Syntax
author: SQLDenis
type: post
date: 2009-03-05T14:06:17+00:00
excerpt: |
  So you want to spit out some XML from SQL Server into a file, how can you do that? There are a couple of ways, I will show you how you can do it with SSIS. In the SSIS package you need an Execute SQL Task and a Script Task.
  
  Let's get started
  
  First&hellip;
url: /index.php/datamgmt/dbprogramming/create-xml-files-out-of-sql-server-with/
views:
  - 100559
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
tags:
  - integration services
  - sql server 2005
  - sql server 2008
  - ssis
  - xml

---
So you want to spit out some XML from SQL Server into a file, how can you do that? There are a couple of ways, I will show you how you can do it with SSIS. In the SSIS package you need an Execute SQL Task and a Script Task.

Let&#8217;s get started

First create and populate these two tables in your database

<pre>create table Artist (ArtistID int primary key not null,
ArtistName varchar(38))
go

create table Album(AlbumID int primary key not null,
ArtistID int not null,
AlbumName varchar(100) not null,
YearReleased smallint not null)
go


insert into Artist values(1,'Pink Floyd')
insert into Artist values(2,'Incubus')
insert into Artist values(3,'Prince')

insert into Album values(1,1,'Wish You Were Here',1975)
insert into Album values(2,1,'The Wall',1979)



insert into Album values(3,3,'Purple Rain',1984)
insert into Album values(4,3,'Lotusflow3r',2009)
insert into Album values(5,3,'1999',1982)


insert into Album values(6,2,'Morning View',2001)
insert into Album values(7,2,'Light Grenades',2006)</pre>

Now create this proc

<pre>create proc prMusicCollectionXML
as
declare @XmlOutput xml 
set @XmlOutput = (select ArtistName,AlbumName,YearReleased from Album
join Artist on Album.ArtistID = Artist.ArtistID
FOR XML AUTO, ROOT('MusicCollection'), ELEMENTS)

select @XmlOutput
go</pre>

After executing the proc

<pre>exec prMusicCollectionXML</pre>

you will see the following output

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

So far so good, so how do we dump that data into a file?
  
Create a new SSIS package add an ADO.NET Connection, name it AdventureWorksConnection
  
Drop an Execute SQL Task onto your control flow and modify the properties so it looks like this

<img src="http://i44.tinypic.com/1zcz3hl.jpg" border="0" alt="Execute SQL Task" />

On the add a result set by clicking on the add button, change the variable name to User::XMLOutput if it is not already like that

Note!!! In SSIS 2008 this variable should be already created otherwise it will fail

<img src="http://i44.tinypic.com/nyab2x.jpg" border="0" alt="Execute SQL Task Adding A Resultset" />

Now execute the package.
  
You will be greeted with the following message:
  
Error: 0xC00291E3 at Execute SQL Task, Execute SQL Task: The result binding name must be set to zero for full result set and XML results.
  
Task failed: Execute SQL Task
   
In order to fix that, change the Result Name property from NewresultName to 0, now run it again and it should execute successfully. 

Our next step will be to write this XML to a file.
  
Add a Script Task to the package,double click the Script Task,click on script and type XMLOutput into the property of ReadWriteVariables. It should look like the image below

<img src="http://i43.tinypic.com/2ug2df7.jpg" border="0" alt="SSIS Script Task" />

Click the Design Script button, this will open up a code window, replace all the code you see with this

<pre>' Microsoft SQL Server Integration Services Script Task
' Write scripts using Microsoft Visual Basic
' The ScriptMain class is the entry point of the Script Task.

Imports System
Imports System.Data
Imports System.Math
Imports Microsoft.SqlServer.Dts.Runtime


Public Class ScriptMain

   

    Public Sub Main()
        '
        ' Add your code here
        '
        Dim XMLString As String = " "



        XMLString = Dts.Variables("XMLOutput").Value.ToString.Replace("&lt;ROOT&gt;", "").Replace("&lt;/ROOT&gt;", "")
        XMLString = "&lt;?xml version=""1.0"" ?&gt;" + XMLString

        GenerateXmlFile("C:\MusicCollection.xml", XMLString)

    End Sub

    Public Sub GenerateXmlFile(ByVal filePath As String, ByVal fileContents As String)

        Dim objStreamWriter As IO.StreamWriter
        Try

            objStreamWriter = New IO.StreamWriter(filePath)

            objStreamWriter.Write(fileContents)

            objStreamWriter.Close()

        Catch Excep As Exception

            MsgBox(Excep.Message)

        End Try
        Dts.TaskResult = Dts.Results.Success
    End Sub

End Class</pre>

**SSIS 2008 requires a code change**
  
Here is what the code should look like if you are running SSIS 2008

<pre>' Microsoft SQL Server Integration Services Script Task
' Write scripts using Microsoft Visual Basic 2008.
' The ScriptMain is the entry point class of the script.

Imports System
Imports System.Data
Imports System.Math
Imports Microsoft.SqlServer.Dts.Runtime

&lt;System.AddIn.AddIn("ScriptMain", Version:="1.0", Publisher:="", Description:="")&gt; _
&lt;System.CLSCompliantAttribute(False)&gt; _
Partial Public Class ScriptMain
	Inherits Microsoft.SqlServer.Dts.Tasks.ScriptTask.VSTARTScriptObjectModelBase

	Enum ScriptResults
		Success = Microsoft.SqlServer.Dts.Runtime.DTSExecResult.Success
		Failure = Microsoft.SqlServer.Dts.Runtime.DTSExecResult.Failure
	End Enum
	

	

	Public Sub Main()
        '
        ' Add your code here
        '
        Dim XMLString As String = " "



        XMLString = Dts.Variables("XMLOutput").Value.ToString.Replace("&lt;ROOT&gt;", "").Replace("&lt;/ROOT&gt;", "")
        XMLString = "&lt;?xml version=""1.0"" ?&gt;" + XMLString

        GenerateXmlFile("C:\MusicCollection.xml", XMLString)

    End Sub

    Public Sub GenerateXmlFile(ByVal filePath As String, ByVal fileContents As String)

        Dim objStreamWriter As IO.StreamWriter
        Try

            objStreamWriter = New IO.StreamWriter(filePath)

            objStreamWriter.Write(fileContents)

            objStreamWriter.Close()

        Catch Excep As Exception

            MsgBox(Excep.Message)

        End Try

        Dts.TaskResult = ScriptResults.Success


    End Sub

End Class</pre>

There are a couple of things you need to know, the XML will be generated inside a <ROOT> tag, I am stripping that out on line 23 of the code, on line 24 I am adding <?xml version=&#8221;1.0&#8243; ?> to the file. Line 26 has the location where the file will be written, right now it is C:MusicCollection.xml but you can modify that.

So now we are all done with this. It is time to run this package. Run the package and you should see that file has been created.