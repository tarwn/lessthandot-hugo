---
title: How to copy data/append data into files from within T-SQL
author: SQLDenis
type: post
date: 2009-07-06T14:17:16+00:00
url: /index.php/datamgmt/datadesign/how-to-copy-data-append-data-into-files/
views:
  - 33800
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - bcp
  - bulk copy
  - data
  - files
  - openrowset

---
Sometimes you want to quickly copy some data into a text file or you want append some data to a text file but you don&#8217;t feel like opening BIDS or the DTS designer. Here is a way to accomplish what you want from within a query window
  
To copy data into a new file use BCP (Bulk Copy Program). To append to a file use OPENROWSET

Here is some sample code. First create this table

<pre>use tempdb
go

create table TestData(Id int, SomeValue varchar(20),SomeOtherValue Decimal(6,2))
go

insert TestData values(1,'abcdefg1',3.31)
insert TestData values(2,'abcdefg2',3.32)
insert TestData values(3,'abcdefg3',3.33)
insert TestData values(4,'abcdefg4',3.34)
insert TestData values(5,'abcdefg5',3.35)
insert TestData values(6,'abcdefg6',3.36)
insert TestData values(7,'abcdefg7',3.37)
insert TestData values(8,'abcdefg8',3.38)
insert TestData values(9,'abcdefg9',3.39)
insert TestData values(10,'abcdefg10',3.40)
insert TestData values(11,'abcdefg11',3.41)
insert TestData values(12,'abcdefg12',3.42)</pre>

Now lets&#8217; first use BCP to copy data into a file. Here is what the command will look like

<pre>master..xp_cmdshell 'bcp "SELECT id, CHAR(34) + SomeValue + CHAR(34),SomeOtherValue FROM tempdb..TestData" queryout C:TestData.txt -t, -c -Slocalhost -T'</pre>

So what does all this stuff do? 

**master..xp_cmdshell** Executes a given command string as an operating-system command shell. 

**bcp** is the bulk copy program

**&#8220;SELECT id, CHAR(34) + SomeValue + CHAR(34),SomeOtherValue FROM tempdb..TestData&#8221;** This is our query, CHAR(34) is the ascii code for double qoutes, we are putting double quotes around the character values/

**queryout** Specifies the direction of the bulk copy

**C:TestData.txt** this is the name of the file that will be created

**-t,** Specifies the field terminator. The default is t (tab character). We are using this parameter to override the default field terminator by using a comma instead.

**-c** Performs the bulk copy operation using a character data type. This option does not prompt for each field

**-Slocalhost** tells bcp where to connect to, in this case localhost.

**-T** Specifies that bcp connects to SQL Server with a trusted connection. If you want to use login credentials and you username is SQL2008 and the password = pw2008 then instead of -T you would do this -U&#8221;SQL2008&#8243; -P&#8221;pw2008&#8243;

There are a lot more arguments available for the BCP utility, I recommend checking all the available arguments out here: http://msdn.microsoft.com/en-us/library/ms162802.aspx



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

Now it is time to execute our query, make sure that everything in the code below is on one line in your query window.

<pre>master..xp_cmdshell 'bcp "SELECT id, CHAR(34) + SomeValue + CHAR(34),SomeOtherValue FROM tempdb..TestData ORDER BY id" queryout C:TestData.txt -t, -c -Slocalhost -T'</pre>

You should see the following output

_NULL
  
Starting copy&#8230;
  
NULL
  
12 rows copied.
  
Network packet size (bytes): 4096
  
Clock Time (ms.): total 1
  
NULL_

Now we will use OPENROWSET to read the file we just created

<pre>select * from OPENROWSET('Microsoft.Jet.OLEDB.4.0', 
'Text;Database=C:;HDR=No;', 'SELECT * FROM TestData.txt')</pre>

If everything is correct and ad-hoc queries are enabled on your instance you should see all the rows we inserted. 

Now let&#8217;s append a row to the file

<pre>INSERT INTO OPENROWSET('Microsoft.Jet.OLEDB.4.0', 
'Text;Database=C:;HDR=Yes;', 'SELECT * FROM TestData.txt')
select 13,'abcdefg13',3.43</pre>

Running this query below will now return 13 rows

<pre>INSERT INTO OPENROWSET('Microsoft.Jet.OLEDB.4.0', 
'Text;Database=C:;HDR=Yes;', 'SELECT * FROM TestData.txt')
select 13,'abcdefg13',3.43</pre>

What if you want to use OPENROWSET to insert into a file that does not exist yet? Let&#8217;s try it out by changing the name to TestData2.txt.

<pre>INSERT INTO OPENROWSET('Microsoft.Jet.OLEDB.4.0', 
'Text;Database=C:;HDR=Yes;', 'SELECT * FROM TestData2.txt')
select 13,'abcdefg13',3.43</pre>

And here is our message
  
_Server: Msg 7399, Level 16, State 1, Line 1
  
OLE DB provider &#8216;Microsoft.Jet.OLEDB.4.0&#8217; reported an error.
  
[OLE/DB provider returned message: The Microsoft Jet database engine could not find the object &#8216;TestData2.txt&#8217;. Make sure the object exists and that you spell its name and the path name correctly.]
  
OLE DB error trace [OLE/DB Provider &#8216;Microsoft.Jet.OLEDB.4.0&#8217; IColumnsInfo::GetColumnsInfo returned 0x80004005: ]._

Mmm, what if we create a file from within a shell command?

<pre>master..xp_cmdshell 'copy nul c:TestData2.txt'</pre>

_1 file(s) copied.
  
NULL
  
_ 

Now that we created the file, let&#8217;s try again

<pre>INSERT INTO OPENROWSET('Microsoft.Jet.OLEDB.4.0', 
'Text;Database=C:;HDR=Yes;', 'SELECT * FROM TestData2.txt')
select * from TestData</pre>

_Server: Msg 7357, Level 16, State 2, Line 1
  
Could not process object &#8216;SELECT * FROM TestData2.txt&#8217;. The OLE DB provider &#8216;Microsoft.Jet.OLEDB.4.0&#8242; indicates that the object has no columns.
  
OLE DB error trace [Non-interface error: OLE DB provider unable to process object, since the object has no columnsProviderName=&#8217;Microsoft.Jet.OLEDB.4.0&#8242;, Query=SELECT * FROM TestData2.txt&#8217;]._

As you can see since the file is an empty file JET doesn&#8217;t know how to insert the data

It might be entirely possible to use OPENROWSET to insert into a blank file and if you know how then feel free to leave a comment. But in general to copy data into a file that does not extist yet use bcp and openrowset to append to a file.
  
If you need do this kind of thing more than once I would recommend to use SSIS (SQL Server Integration Services) or DTS (Data Transformation Services) since it is much more flexible and customizable.



\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][2] forum**<ins></ins>

 [1]: http://forum.ltd.local/viewforum.php?f=17
 [2]: http://forum.ltd.local/viewforum.php?f=22