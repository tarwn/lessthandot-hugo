---
title: Working with 32 bit providers and 64 bit SQL Server
author: Naomi Nosonovsky
type: post
date: 2010-08-12T14:39:48+00:00
excerpt: |
  UPDATE
  Turned out I was wrong and it's easy enough to create SSIS package to run an import of VFP table into SQL Server 64 bit. The complete solution was provided by Jin Chen in this MSDN thread How to run SSIS package - Import VFP table to SQL Server&hellip;
url: /index.php/datamgmt/datadesign/think-carefully-which-sql-server-you-wan/
views:
  - 55428
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
### <span class="MT_red">UPDATE</span>

Turned out I was wrong and it&#8217;s easy enough to create SSIS package to run an import of VFP table into SQL Server 64 bit. The complete solution was provided by Jin Chen in this MSDN thread [How to run SSIS package &#8211; Import VFP table to SQL Server 64 bit][1] and I was able to easily follow it and the package ran OK. I now quote the solution:

In order to load data from Visual FoxPro to 64-bit SQL Server, we can use Microsoft OLE DB Provider for Visual FoxPro or Visual FoxPro ODBC Driver to load data from Visual FoxPro, and then upload into SQL Server.

Below are the detail steps for your reference:
  
Before starting the steps, please make sure the Microsoft OLE DB Provider for Visual FoxPro and Visual FoxPro ODBC Driver is installed.
  
Solution1: Use the Microsoft OLE DB Provider for Visual FoxPro to load data from Visual FoxPro database

Create a new SQL Server Integration Services(SSIS) package.
  
Add a Data Flow Task(DFT) to the package.
  
Select the DFT, and go to Data Flow designer page.
  
Add a OLE DB Source to the package.
  
Double-click the OLE DB Source, click &#8220;New&#8221; > &#8220;New&#8221; to create a connection to Visual FoxPro.
  
In the Connection Manager, please select &#8220;Microsoft OLE DB Provider for Visual FoxPro&#8221;
  
Type the file path of a Visual FoxPro database(e.g. C:Address.dbc).
  
Test the connection by clicking button &#8220;Test Connection&#8221;
  
Click &#8220;OK&#8221; > &#8220;OK&#8221; to apply.
  
Now, we can select a table from the Visual FoxPro database in the OLE DB Source editor.
  
Solution2: Use the Visual FoxPro ODBC Driver to load data from Visual FoxPro database

As you done, create a DSN pointing to VFP free table using odbcad32 from SysWOW64 folder. I would suggest you using System DSN instead of User DSN.
  
Create a new SQL Server Integration Services(SSIS) package.
  
Add a Data Flow Task(DFT) to the package.
  
Select the DFT, and go to Data Flow designer page.
  
Add an ADO NET Souce to the package. Please note, ADO NET is started to support from SSIS 2008. In other words, in SSIS 2005, there is no ADO NET source.
  
Double-click the OLE DB Source, click &#8220;New&#8221; > &#8220;New&#8221; to create a connection to Visual FoxPro.
  
In the Connection Manager, please select &#8220;Odbc Data Provider&#8221;
  
Select the DSN we created before, and then test the connection, make sure it is successful.
  
Click &#8220;OK&#8221; > &#8220;OK&#8221; to apply.
  
Now, we can select a table from the Visual FoxPro database in the ADO NET Source editor. If it is fail, please try using SQL Command mode.
  
Additionally, we strongly recommend using the Visual FoxPro OLE DB provider as a replacement. Also, in design-time, pleaes set the package to run in 32-bit mode, otherwise the package will fail to run.
  
Right-click the project > &#8220;Properties&#8221; > &#8220;Debugging&#8221; > Set &#8220;Run64Runtime&#8221; to be &#8220;False&#8221;.

The two drivers can be downloaded from:
  
http://www.microsoft.com/downloads/en/details.aspx?familyid=e1a87d8f-2d58-491f-a0fa-95a3289c5fd4&displaylang=en
  
http://msdn.microsoft.com/en-us/vfoxpro/bb190233.aspx

See also this very helpful blog post by Todd McDermid
  
[Quick Reference: SSIS in 32- and 64-bits][2]

Also important suggestion by Scott Stauffer from the comments
  
If you run the package interactively in BIDS, right click the project and choose properties, choose Debugging on the left of the properties dialogue box, then change Run64BitRuntime from &#8220;True&#8221; to &#8220;False&#8221;. I would suspect this should work. 

To run the package from a SQLAgent Job, go to the properties of the step of a type SQL Server Integration Services Package, and click on the Execution Options tab, then choose &#8220;Use 32 bit runtime&#8221;

This is also an important [relevant thread][3] 

Bellow is the original text of the blog, which is not really relevant anymore.
  
&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;
  
In this short blog post I&#8217;d like to emphasize the importance of a decision which SQL Server version to install &#8211; 32 bit or 64 bit in 64 bit OS. If you install 64 bit version of SQL Server, be aware, that you may not be able to utilize data from many non SQL sources, that don&#8217;t have OleDB or ODBC driver in 64 bit version.

Say, you&#8217;ll get the following error attempting to invoke Import/Export wizard for VFPOleDB:

TITLE: SQL Server Import and Export Wizard
  
&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;

The operation could not be completed.

&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;
  
ADDITIONAL INFORMATION:

Feature is not available. (Microsoft OLE DB Provider for Visual FoxPro)

&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;
  
BUTTONS:

OK
  
&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;

===================================

===================================

Feature is not available. (Microsoft OLE DB Provider for Visual FoxPro)

And if you try to expand this error, you&#8217;ll get this information:
  
&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;
  
Program Location:

at System.Data.OleDb.OleDbConnectionInternal..ctor(OleDbConnectionString constr, OleDbConnection connection)
  
at System.Data.OleDb.OleDbConnectionFactory.CreateConnection(DbConnectionOptions options, Object poolGroupProviderInfo, DbConnectionPool pool, DbConnection owningObject)
  
at System.Data.ProviderBase.DbConnectionFactory.CreateNonPooledConnection(DbConnection owningConnection, DbConnectionPoolGroup poolGroup)
  
at System.Data.ProviderBase.DbConnectionFactory.GetConnection(DbConnection owningConnection)
  
at System.Data.ProviderBase.DbConnectionClosed.OpenConnection(DbConnection outerConnection, DbConnectionFactory connectionFactory)
  
at System.Data.OleDb.OleDbConnection.Open()
  
at Microsoft.SqlServer.Dts.DtsWizard.DTSWizard.GetOpenedConnection(WizardInputs wizardInputs, String connEntryName)
  
at Microsoft.SqlServer.Dts.DtsWizard.Step1.OnLeavePage(LeavePageEventArgs e)

&#8212;&#8212;&#8212;&#8212;&#8212;
  
None of the methods outlined in this WiKi article
  
[Visual FoxPro Data From SQL Server][4] will work for you.

Just something to be aware of.

[Here][5] is a reply by MSFT employee on this topic:
  
_There is no 64-bit VFP OleDB provider. That is why we can&#8217;t import data from VFP to a 64-bit SQL Server. The workaround in the link you provided won&#8217;t work too, as it is using the VFP provider too. This can&#8217;t work in 64bit SQL Server.</p> 

In order to work around the issue, we can firstly install a 32-bit SQL Server, import the data from VFP to this 32-bit SQL Server, and then synchronize the data from the 32-bit SQL Server to the 64-bit SQL Server.
  
This workaround is available too in other scenarios that there is no 64-bit provider and we are using 64-bit SQL Server.</i>

In other words, if you need working with data in other formats, use 32 bit version of SQL Server.

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][6] forum or our [Microsoft SQL Server Admin][7] forum**<ins></ins>

 [1]: http://social.msdn.microsoft.com/Forums/en-US/sqlintegrationservices/thread/c32c0820-0e2d-4461-820a-530541c769c6
 [2]: http://toddmcdermid.blogspot.com/2009/10/quick-reference-ssis-in-32-and-64-bits.html
 [3]: http://social.msdn.microsoft.com/Forums/en-US/transactsql/thread/d730190c-47e6-4c00-91c0-3e95af0fdc5e
 [4]: http://fox.wikis.com/wc.dll?Wiki~VisualFoxProDataFromSQLServer
 [5]: http://social.msdn.microsoft.com/Forums/en-US/sqltools/thread/ad87945a-b47f-4ec5-941a-d2ec7b02be59
 [6]: http://forum.lessthandot.com/viewforum.php?f=17
 [7]: http://forum.lessthandot.com/viewforum.php?f=22