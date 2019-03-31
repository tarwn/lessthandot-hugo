---
title: SQL Server DBA Tip 9 – SQL Server Monitoring – Default Trace
author: Ted Krueger (onpnt)
type: post
date: 2011-05-06T10:31:00+00:00
excerpt: 'The Default Trace exists in SQL Server 2005 and higher versions, in all editions.  The trace has been questioned: what is it, and why is it helpful?  We will go over the answers to these questions and look into the Default Trace, showing how to use it t&hellip;'
url: /index.php/datamgmt/dbadmin/sql-server-dba-tip-9/
views:
  - 9597
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
The Default Trace exists in SQL Server 2005 and higher versions, in all editions.  The trace has been questioned: what is it, and why is it helpful?  We will go over the answers to these questions and look into the Default Trace, showing how to use it to retrieve information it collects while enabled.

**What is the Default Trace?**

The Default Trace is a trace prepackaged into SQL Server and enabled by default when you install SQL Server.  You cannot alter the events the Default Trace captures but can disable the trace by executing the following.

The Default Trace’s value may provide a reason to leave the trace enabled.  The code below is provided to show of how to disable it is provided in case the need arises for it to be disabled.

 

<pre>sp_configure 'show advanced options', 1
GO
RECONFIGURE
GO
sp_configure 'default trace enabled', 0
GO
RECONFIGURE
GO</pre>

 

The Default Trace tracks events that revolve around configurations changes and collects information on changes such as ALTER object events and login failures.  Since the trace is not documented thoroughly in Books Online, we need to execute a query to determine the events that are being captured.  This is done by using fn\_trace\_geteventinfo, fn\_trace\_getinfo, sys.trace\_events and sys.trace\_columns as shown below.

 

<pre>SELECT 
	TraceEvents.name as [Event], 
	TraceData.name as [Data Collected]
FROM ::fn_trace_geteventinfo((SELECT TOP 1 TraceID FROM ::fn_trace_getinfo(default))) TraceInfo
JOIN sys.trace_events TraceEvents ON TraceInfo.eventID = TraceEvents.trace_event_id
JOIN sys.trace_columns TraceData ON TraceInfo.columnid = TraceData.trace_column_id</pre>

 

The results of the unique event names

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-46.png?mtime=1303838288"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-46.png?mtime=1303838288" width="358" height="261" /></a>
</div>

 

As with other traces, not all columns have available data pertaining to the specific event.  If a value is NULL, it is likely that the information is not available for the captured event.  This can be verified by using the query shown above, <span style="text-decoration: line-through;">by</span> checking the Data Collected values returned.

 

**Default Trace in action – Why is it helpful?**

** **

When working in a DBA Team, it can be important to know when something was changed and who made the change, in SQL Server.  This information is used for auditing and troubleshooting. 

 

Here we will test the capture of TABLE CREATE, ALTER and DROP running the following with the default trace enabled.

 

<pre>CREATE TABLE Junked (COL INT)
GO
ALTER TABLE Junked
 ADD COL2 VARCHAR(10)
GO
DROP TABLE Junked
GO</pre>

 

To query the event that was forced above, we can use the fn\_trace\_gettable to load the trace file into a table format.  The trace file that is current can be found by running the fn\_trace\_getinfo.

 

<pre>SELECT * FROM ::fn_trace_getinfo(default)</pre>

 

Load the file that was represented in the property value 2 row returned.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-47.png?mtime=1303838288"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-47.png?mtime=1303838288" width="482" height="128" /></a>
</div>

 

Then use the file path as:

 

<pre>SELECT 
	TraceInfo.ServerName,
	TraceInfo.StartTime,
	TraceInfo.LoginName,
	TraceInfo.ObjectName,
	TraceEvents.name
FROM 
::fn_trace_gettable('C:SQL2008R2MSSQL10_50.TK2008R2MSSQLLoglog_257.trc',0) TraceInfo
JOIN sys.trace_events TraceEvents ON TraceInfo.eventclass = TraceEvents.trace_event_id
WHERE ObjectName = 'Junked'</pre>

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-49.png?mtime=1303838526"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-49.png?mtime=1303838526" width="529" height="113" /></a>
</div>

 

From the illustration above, we can see that the table “Junked” was created, altered and then deleted.  This information can be invaluable in the event a table was dropped or altered, in the event that change caused a severe problem to the functionality of the table.  The time that the table was changed can show a specific set of events that may have led up to it, or reasoning they occurred.  Further value is seen in audit reporting on production systems that require change control. 

 