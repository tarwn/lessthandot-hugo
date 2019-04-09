---
title: Derived Column and the Data Flow Task
author: Ted Krueger (onpnt)
type: post
date: 2009-10-08T00:47:00+00:00
ID: 580
url: /index.php/datamgmt/datadesign/derived-column-and-the-data-flow-task/
views:
  - 8610
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
In many cases you will find yourself in the position of having multiple data sources that you need to bring into one centralized destination. In that transport, many times over you will need to add columns for identifiers or other pointers that are required for later usage. This as you can see, starts going down the warehouse loading discussion. For this I don’t want to go that far into it though. I want to show a brief and simple example you can run through in order to give you an idea of the power behind adding key indicators to those imports quickly with little amount of change to the source.

In this example we have two external MS Access databases that are loaded individually but unfortunately, data that can be redundant across these types of setups. This brings a need for us to import those two sources into our one source while giving each a unique key in order to track it. To create that unique key we have several options. We could do this all in TSQL and linked server entries, import and update statements, send the key with the source and on. The linked servers option would actually be quick and an easy development task but linked servers have their own issues and we want to stay away from that. SSIS has plenty of options for us to use so we package, distribute and create an easily maintained process. In SSIS we could execute TSQL tasks to get the key in there or something I would choose over most options, adding a data flow task and using derived columns for our keys. 

I think derived column objects in SSIS are just cool. When you think about it, the concept of being able to put columns into your sources dynamically at runtime like this is powerful. It’s easy to see the relational values that you can compile with these objects. To show this I’m going to bring both of those MS Access databases into an SSIS package. Then given a derived column and some data conversion, we’ll insert that data directly into a SQL Server table for later use. 

The example will have the following resources

<span class="MT_red">Note: I am doing this on a 64bit machine with Access 2007 only but this will transfer to earlier versions. The biggest difference will be the final execution of the SSIS package and having the requirement to use DTEXEC for 32bit runtime mode. </span>

## _Preparing the objects…_

The MS Access databases are named Database1.accdb and Database2.accdb.

The tables in each are identical and appear as follows

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//access.gif" alt="" title="" width="628" height="98" />
</div>

To create our supporting SQL Server table run the following create statement

sql
SET ANSI_NULLS ON
GO

SET QUOTED_IDENTIFIER ON
GO

SET ANSI_PADDING ON
GO

CREATE TABLE [dbo].[SQLSource](
	[ident] [int] NULL,
	[source_ident] [int] NULL,
	[FirstName] [varchar](50) NULL,
	[lastName] [varchar](50) NULL,
	[Address] [varchar](100) NULL,
	[BirthDate] [Date] NULL
) ON [PRIMARY]

GO

SET ANSI_PADDING OFF
GO
```

We now know what we have to work with. Let’s get going on the process of importing the data into SQL Server.

In a new SSIS solution, first start by setting up 4 variables. The variables will hold our MS Access database locations and the relational key that identifies them

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//variables.gif" alt="" title="" width="614" height="172" />
</div>

Bring over two data flow tasks now to the control flow window. Name them Source 1 and Source 2 and double click Source 1 to go into the data flow designer.

## _Set up connections…_

Right click the connection manager window and click new OLE DB Connection. Make this your SQL Server connection. Then right click connection manager window and click OLE DB Connection again. This connection will act as our source 1 MS Access database. 

Drop down the OLE DB Provider list and select either jet 4 or 12.0 in my case for 2007. Enter the location and click OK to save the connection. Repeat those steps to create Source 2. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//connmdb.gif" alt="" title="" width="614" height="623" />
</div>

Now highlight Source 1 so you can edit the properties of it. In the expressions section click the ellipse to open the properties window. Drop down the selection and select ConnectionString. For this we will add an expression as follows

```
"Data Source=" + @[User::source1] + ";Provider=Microsoft.ACE.OLEDB.12.0;"
```

This will create a valid connection string to the Access database. Also, add a ServerName property which will map to the same source1 variable. When you are done you should have a properties window as such

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//strings.gif" alt="" title="" width="618" height="199" />
</div>

Repeat the steps again for the source 2 connection.

## _The import itself…_

Bring over an OLE DB Source into the data flow window and configure it to use source1 connection manager and tblSource1. Select all of the columns in the Columns selection and hit ok to save the configuration. 

Now bring over the Derived Column object and double click it. The Derived column transformation editor is pretty good at making things easy for you. Basically what we want is a column that will map to our “ident” column in SQL Server and hold the value we placed in the variable identifying this source. 

To do this enter “ident” into the Derived Column Name, leave “add as new column” and then expand the variables section above that to expose your user variables. You can now drag the variable source1key into the Expression place holder. Once you do this the value will be parsed and the data type will be populated for you. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//dragvariables.gif" alt="" title="" width="628" height="241" />
</div>

Next we need to make sure data types are handled. To do that bring over a Data Conversion task and connect the Derived Column to it. Double click the data conversion to select what we want and how to convert the values. The main thing we need to worry about is the Unicode conversion in this example. So change the DT\_WSTR to DT\_STR for all the string based columns. Alter the lengths to match also. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//columns.gif" alt="" title="" width="628" height="535" />
</div>

Now bring over an OLE DB Destination and connect the data conversion to it. Select the table we created prior and go into the mappings. Map our converted copies to the table columns and click OK to save.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//mappings.gif" alt="" title="" width="628" height="535" />
</div>

That’s pretty much all we need to do at this point to accomplish the task. Repeat everything for source 2 to get ready to execute the build of the package.

The data flow should look like this when all done

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//finished.gif" alt="" title="" width="556" height="361" />
</div>

Most of you running 64bit job servers or test systems with SSIS know that you can’t just click execute now. You have to use DTEXEC so you run in 32bit mode. To do this you can build the solution so your package is created in the bin folder. Then by running the following command in command prompt

```
DTEXEC /f “C:MultiImport.dtsx”
```

Once executing that command you should receive the following return

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//executeresults.gif" alt="" title="" width="628" height="288" />
</div>

And checking our table we should see the data and identification of the source that it came from

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//theresults.gif" alt="" title="" width="523" height="176" />
</div>