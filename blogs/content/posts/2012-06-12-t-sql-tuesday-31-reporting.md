---
title: 'T-SQL Tuesday #31 – Reporting Services Logs'
author: Jes Borland
type: post
date: 2012-06-12T11:58:00+00:00
excerpt: It’s the second Tuesday of the month, and that means it’s T-SQL Tuesday! This month’s topic, brought to us by Aaron Nelson (twitter | blog), is Logging.
url: /index.php/datamgmt/dbadmin/t-sql-tuesday-31-reporting/
views:
  - 6289
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
  - SSRS

---
It’s the second Tuesday of the month, and that means it’s T-SQL Tuesday! This month’s topic, brought to us by Aaron Nelson ([twitter][1] | [blog][2]), is Logging.

<p style="text-align: center;">
  <a href="http://sqlvariant.com/2012/06/t-sql-tuesday-31-logging/"><img src="http://sqlblog.com/blogs/argenis_fernandez/TSQL2sDay150x150_thumb_2AA4EA0F.jpg" alt="" /></a>
</p>

SQL Server logs many, many pieces of information. All of these are valuable when there are problems with the server. What happens when there is a problem with a Reporting Services instance or report, though? Those errors are not written to the SQL Server error log. Where can you go to find more information?

**dbo.ExecutionLog3** 

The Report Server database (in 2008R2 and 2012) contains the view dbo.ExecutionLog3. (SQL Server 2008 has dbo.ExecutionLog2.) This view will give you information about reports that are executed on the server. Some of the information you can get is: request type (ad-hoc or subscription), format requested, parameter(s) selected, and how long the report took to run (broken down by data retrieval, processing time, and rendering time).

Doesn’t that sound fantastic? It is! It’s very simple to run, too.

 

<pre>USE ReportServer;
GO

SELECT InstanceName, ItemPath, UserName, ExecutionId, RequestType, Format, Parameters, ItemAction, TimeStart, TimeEnd, TimeDataRetrieval, TimeProcessing, TimeRendering, Source, Status, ByteCount, [RowCount], AdditionalInfo
FROM ExecutionLog3;</pre>

 

Your results will look like this:

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/TSQL31ExecLog.JPG?mtime=1339508905" alt="" />
</p>

You can get more information about this view [here][3].

**Reporting Services Log Files** 

But what if you have a problem not directly related to the execution of a report? Perhaps a user can’t access the site, or a report returns an error about low memory. Reporting Services writes errors to a set of log files. These can be accessed at C:Program FilesMicrosoft SQL ServerMSRS10\_50.MSSQLSERVERReporting ServicesLogFiles. The files will be named ReportServerService\__timestamp_.

System information will be stored here. You can also find events, exceptions, and low resource warnings.

The information here is far more detailed. An example from my SQL Server 2008R2 instance is:

`servicecontroller!DefaultDomain!bd0!06/11/2012-19:07:22:: e ERROR: Error initializing configuration from the database: Microsoft.ReportingServices.Library.ReportServerDatabaseUnavailableException: The report server cannot open a connection to the report server database. A connection to the database is required for all requests and processing. ---> System.Data.SqlClient.SqlException: Cannot open database "ReportServer" requested by the login. The login failed.<br />
Login failed for user 'geek_grrl-THINKSSRS'.<br />
at System.Data.SqlClient.SqlInternalConnection.OnError(SqlException exception, Boolean breakConnection)<br />
at System.Data.SqlClient.TdsParser.ThrowExceptionAndWarning(TdsParserStateObject stateObj)<br />
at System.Data.SqlClient.TdsParser.Run(RunBehavior runBehavior, SqlCommand cmdHandler, SqlDataReader dataStream, BulkCopySimpleResultSet bulkCopyHandler, TdsParserStateObject stateObj)`

So, when looking for information about why a report failed, or if there appear to be problems with your report server, this can be a place to find valuable information. You can read more about the log file [here][4].

**Troubleshooting – An Essential Skill** 

An essential skill for any IT professional is the ability to troubleshoot a situation. Common sense and remaining calm will take you far. The next step is knowing what tools you have available to you. In Reporting Services, make sure you are taking advantage of the logging tools provided!

 [1]: http://twitter.com/sqlvariant
 [2]: http://sqlvariant.com/
 [3]: http://sqlvariant.com/2012/06/t-sql-tuesday-31-logging/
 [4]: http://msdn.microsoft.com/en-us/library/ms156500.aspx