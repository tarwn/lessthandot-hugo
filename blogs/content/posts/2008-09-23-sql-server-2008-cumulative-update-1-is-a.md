---
title: SQL Server 2008 Cumulative Update 1 is available from Microsoft
author: SQLDenis
type: post
date: 2008-09-23T09:49:29+00:00
ID: 151
url: /index.php/datamgmt/datadesign/sql-server-2008-cumulative-update-1-is-a/
views:
  - 44007
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - cumulative update
  - hotfix
  - sql
  - sql server 2008

---
Cumulative Update 1 contains hotfixes for the Microsoft SQL Server 2008 issues that have been fixed since the release of SQL Server 2008.

You can request this SQL Server 2008 Cumulative Update here: http://support.microsoft.com/default.aspx/kb/956717

Below is the long list of things that have been fixed

_When you use a SAP client computer to view a SQL Server database, the database may be smaller than expected/kbIn SQL Server 2008, a query returns incorrect results for the Date data type when you run the query on a partitioned table that uses data compression on only one of the partitions</p> 

A mirror server cannot fail over to a principal server in a SQL Server 2008 failover cluster

You receive an error message that states that the broker GUIDs do not match when you try to establish database mirroring in SQL Server 2008 or in SQL Server 2005

An XML query that contains the @Table variable or the @XML variable takes a long time to complete in SQL Server 2008

When you query through a view that uses the ORDER BY clause in SQL Server 2008, the result is still returned in random order

The result for the Sum or Count function returns an empty value when you query a SQL Server 2008 Analysis Services cube

Some lists that use visibility toggles may not work as expected when you upgrade to SQL Server 2008 Reporting Services

Some text characters are displayed as square glyphs in a report that you design or view by using SQL Server 2008 Reporting Services

Error message when you try to run or preview a very large report in Microsoft SQL Server 2008 Reporting Services: “Exception of type System.OutOfMemoryException was thrown”

Error message when you try to view a report in SQL Server 2008 Reporting Services: “Object reference not set to an instance of an object. (rsRuntimeErrorInExpression)”

SQL Server 2008 Reporting Services stops responding when you try to render a report in the Image format

When you try to run a report that has recursive hierarchy and that uses toggles in the hierarchy, you may receive unpredictable results in SQL Server 2008 Reporting Services

A dump file may be generated in the Reporting Services Logfiles folder and you may receive an error message when you try to render a report in SQL Server 2008 Reporting Services

The Gauge report item uses only one point after you add multiple points to the item in SQL Server 2008 Reporting Services

Some items are split over two pages when you export a report to a PDF in SQL Server 2008 Reporting Services and you set the KeepTogether property

The embedded Tablix row and column size is not 100% for cells that contain data when a Tablix is embedded inside another Tablix in SQL Server 2008 Reporting Services

An image that is nested in a Tablix cell in SQL Server 2008 Reporting Services is displayed incorrectly when the Sizing property of the image is set to AutoSize

Images in rows are not displayed correctly for static Tablix members in a SQL Server 2008 Reporting Services report

You receive an error message when you use the LoadReportDefinition API to view a drillthrough report or a subreport report in Report Builder in SQL Server 2008 Reporting Services

SQL Server 2008 Reporting Services interprets the Slant property as a positive slope

SQL Server 2008 Reporting Services may stop responding when you move a history snapshot or a linked report to a new folder location

Error message when you try to create a query against a Hyperion Essbase cube database: “OLAP error (1260046): Unknown Member PARENT\_UNIQUE\_NAME used in query”

Error message when you run an MDX query that uses the CREATE GLOBAL CUBE statement to create a local cube in SQL Server 2008 Analysis Services or in SQL Server 2005 Analysis Services: “The syntax for &#8216;<MDXFunctionName>' is incorrect”

The SQL Server 2005 Distribution Agent and the SQL Server 2008 Distribution Agent do not skip error 20598 when you configure a SQL Server 2000 transactional publication to skip error 20598

In SQL Server 2008 or in SQL Server 2005, the session that runs the TRUNCATE TABLE statement may stop responding, and you cannot end the session

You cannot edit a component that has multiple inputs in a SQL Server 2008 or SQL Server 2005 Integration Services package

An incorrect aggregate value is returned when you run an MDX query against a parent/child dimension that has the HideMemberIf property set to ParentIsBlankSelfOrMissing in SQL Server 2008 or in SQL Server 2005

Error message when you run an INSERT statement in SQL Server: “Cannot insert an explicit value into a timestamp column”

You cannot view calculated members in an Excel 2007 pivot table that references an OLAP table in SQL Server 2005 Analysis Services Service Pack 2 or in SQL Server 2008 Analysis Services

Error message when you run an MDX query in SQL Server 2008 Analysis Services: “The MDX function failed because the coordinate for the &#8216;<AttributeName>' attribute contains a set”

Error message when you update a table at the subscriber in a transactional replication in SQL Server 2005: “Updateable Subscriptions: Rows do not match between Publisher and Subscriber”

No rows are returned when you use the sp_replqueuemonitor stored procedure to list the queued messages for a queue-updating subscription in SQL Server

When you update rows by using a cursor in SQL Server 2005, the update may take a long time to finish

Error message when you process a partition from SQL Server 2005 Analysis Services Cumulative Update 7: “Detected inconsistency between User define slice and detected slice of partition. The slice specified for the %{Property/} attribute is incorrect”

You receive a “Current session is no longer valid” error message or an access violation occurs when you process a cube in SQL Server 2008 Analysis Services

Error message when you run an UPDATE statement against a table that has a DML trigger in SQL Server 2008: “An inconsistency was detected during an internal operation”

Analysis Services may stop responding when a NonEmpty clause causes many cell errors

A SQL Server 2008 Analysis Services server may stop responding when you try to run an MDX query that uses cell security
  
50003352 957820 (http://support.microsoft.com/kb/957820/) FIX: The mining model opens a blank task editor window when you try to deploy the Data Mining Model Training task in a SQL Server 2008 Integration Services project in Business Intelligence Development Studio

You cannot merge a publication that has spatial types that include large values with a SQL Server CE subscriber

A MERGE statement may not enforce a foreign key constraint when the statement updates a unique key column that is not part of a clustering key and there is a single row as the update source in SQL Server 2008

The IntelliSense feature does not work correctly for a database that uses the Turkish collation and that uses the case-insensitive collation sort order in SQL Server 2008

A NullReferenceException error for T-SQL Debugger occurs when you use the SET SHOWPLAN_XML ON option in a Transact-SQL statement on a instance of SQL Server 2008

The IntelliSense feature incorrectly completes the logical operators in the CASE WHEN clause in SQL Server 2008

You receive an error message when you click the View Single Column Profile By Column button in the Data Profile Viewer in SQL Server 2008: “Unhandled Exception”

The IntelliSense feature stops working when you type a query that has a specific lexical combination in SQL Server Management Studio in SQL Server 2008

You cannot start the Data Profile Viewer (DataProfileViewer.exe) in SQL Server 2008

In SQL Server 2008, the IntelliSense feature may become unavailable when you type Transact-SQL statements in the Query Editor in SQL Server Management Studio

Error message when you start the SQL Server 2008 Reporting Services Configuration Manager: “Invalid namespace”

The SimSun font is not displayed correctly when you view a SQL Server 2008 Report Services report

Clients cannot connect to the Report Server Web service in SQL Server 2008 Reporting Services

Error message when you render a report in SQL Server 2008 Reporting Services: “A generic error occurred in GDI+”

Data disappears from a report that contains an inner group that is set to an initial state of “Hidden” in SQL Server 2008 Reporting Services

A SQL Server 2008 Reporting Services report may repaginate continually and consume 100% of CPU resources

No records may be returned when you call the SQLExecute function to execute a prepared statement and you use the SQL Native Client ODBC Driver in SQL Server 2008

Error message when you install SQL Server 2008 Express edition on a domain controller that is running Windows Server 2003 SP2 or Windows Small Business Server 2003 SP1: “Exception has been thrown by the target of invocation”

You cannot upgrade a non-English instance of SQL Server 2000 Reporting Services to SQL Server 2008 Reporting Services

When you try to run SQL Server 2008 database engine repair from a DVD, the repair fails, and you may receive error messages

When you install SQL Server 2008, the installation fails, and the “Attributes do not match” error message is logged in the Summary.txt file

SQL Server Setup does not use the instance name that you provide in the modified Config.ini file when you try to install SQL Server 2008 Express

Data warehousing may stop collecting data when many time-outs occur

You receive a memory error message when you try to run SQL Server Setup from a network resource

You may not be able to uninstall a prepared instance of SQL Server 2008

Cluster upgrade to SQL Server 2008 fails when SQL Server 2005 cluster nodes have different installed features

When you install a clustered instance of SQL Server 2008, account validation fails even though you have specified the correct domain account and password

The CompleteFailoverCluster action does not detect the correct SKU that is prepared by using the PrepareFailoverCluster action in SQL Server 2008

Error message when you try to add a second node to a SQL Server 2008 failover cluster: “The current SKU is invalid”

You receive an error message that the file is being used by another process when you try to rename or move an output file from a job in SQL Server Agent in SQL Server 2008

A new node is not added to a SQL Server 2008 Analysis Services cluster or a SQL Server 2008 Reporting Services cluster if the SQL Server 2008 Database Engine is not installed

Event ID 7904 is logged, and the SQL Server 2008 database is corrupted when you restore a SQL Server 2008 database from a sequence of transaction log backups

When you programmatically retrieve data from a SQL Server 2008 database, the data in memory may be overwritten

The Service Broker connections between endpoints may experience a deadlock condition when the intiator queues contain many messages

The clustered index table may take longer than you expect to be rebuilt when you use the ALTER INDEX REBUILD statement in SQL Server 2008

A SQL Server 2008 query returns incorrect results when you create a spatial index in a table that contains a composite primary key

The Database Mirroring feature does not work on a SQL Server 2008-based mirror server when you perform a rolling upgrade on the mirror server

Extended Events session produces an empty value for data captured for certain actions

The Processes pane in the Activity Monitor incorrectly shows sessions as head blockers in SQL Server 2008

When a SQL Server 2008 policy file executes a WMI query, the policy always executes the query against the local server regardless of the intended target

You cannot expand the Facets node under the Policy Management node in SQL Server Management Studio in a non-English version of SQL Server 2008</em>