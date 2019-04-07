---
title: How to Monitor Database Mirroring
author: ptheriault
type: post
date: 2009-10-15T10:41:06+00:00
ID: 588
excerpt: 'This morning I came into work and went through my usual 100 or so emails.  One of the emails was from MSSQLTips.com, it was on how to monitor SQL Server Database mirroring with email alerts. By Alan Cranfield.  While agree with Alan that every DBA shoul&hellip;'
url: /index.php/datamgmt/dbadmin/how-to-monitor-database-mirroring/
views:
  - 28931
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration

---
This morning I came into work and went through my usual 100 or so emails. One of the emails was from MSSQLTips.com, it was on how to monitor SQL Server Database mirroring with email alerts. By Alan Cranfield. While agree with Alan that every DBA should monitor their database mirroring with email alerts I disagreed with his method. He had the DBA create a job that was scheduled to run at some interval throughout the day. His job would query the sys.database_mirroring view. As DBAs we need to know immediately when something fails or changes. 5 minutes could be the difference between a quick fix and restoring a 500 GB db mirror. 

So what would be a better way to monitor and alert a DBA when there is a change in the state of Database mirroring? I prefer to use Alerts for events. Event notifications can be created directly in the SQL Server Database Engine or by using the WMI Provider for Server Events. A DBA can specify which db mirroring event they wish to moitor. Here is a table of events to monitor for:

<table style="border:1pt solid #000;">
  <tr style="background-color:#CCCCCC;">
    <th style="border:1pt solid #000;">
      State
    </th>
    
    <th style="border:1pt solid #000;">
      Name
    </th>
    
    <th style="border:1pt solid #000;">
      Description
    </th>
  </tr>
  
  <tr>
    <td style="border:1pt solid #CCC;">
    </td>
    
    <td style="border:1pt solid #CCC;">
      Null Notification
    </td>
    
    <td style="border:1pt solid #CCC;">
      This state occurs briefly when a mirroring session is started.
    </td>
  </tr>
  
  <tr>
    <td style="border:1pt solid #CCC;">
      1
    </td>
    
    <td style="border:1pt solid #CCC;">
      Synchronized Principal with Witness
    </td>
    
    <td style="border:1pt solid #CCC;">
      This state occurs on the principal when the principal and mirror are connected and synchronized and the principal and witness are connected. For a mirroring configuration with a witness, this is the normal operating state.
    </td>
  </tr>
  
  <tr>
    <td style="border:1pt solid #CCC;">
      2
    </td>
    
    <td style="border:1pt solid #CCC;">
      Synchronized Principal without Witness
    </td>
    
    <td style="border:1pt solid #CCC;">
      This state occurs on the principal when the principal and mirror are connected and synchronized but the principal does not have a connection to the witness. For a mirroring configuration without a witness, this is the normal operating state.
    </td>
  </tr>
  
  <tr>
    <td style="border:1pt solid #CCC;">
      3
    </td>
    
    <td style="border:1pt solid #CCC;">
      Synchronized Mirror with Witness
    </td>
    
    <td style="border:1pt solid #CCC;">
      This state occurs on the mirror when the principal and mirror are connected and synchronized and the mirror and witness are connected. For a mirroring configuration with a witness, this is the normal operating state.
    </td>
  </tr>
  
  <tr>
    <td style="border:1pt solid #CCC;">
      4
    </td>
    
    <td style="border:1pt solid #CCC;">
      Synchronized Mirror without Witness
    </td>
    
    <td style="border:1pt solid #CCC;">
      This state occurs on the mirror when the principal and mirror are connected and synchronized but the mirror does not have a connection to the witness. For a mirroring configuration without a witness, this is the normal operating state.
    </td>
  </tr>
  
  <tr>
    <td style="border:1pt solid #CCC;">
      5
    </td>
    
    <td style="border:1pt solid #CCC;">
      Connection with Principal Lost
    </td>
    
    <td style="border:1pt solid #CCC;">
      This state occurs on the mirror server instance when it cannot connect to the principal.
    </td>
  </tr>
  
  <tr>
    <td style="border:1pt solid #CCC;">
      6
    </td>
    
    <td style="border:1pt solid #CCC;">
      Connection with Mirror Lost
    </td>
    
    <td style="border:1pt solid #CCC;">
      This state occurs on the principal server instance when it cannot connect to the mirror.
    </td>
  </tr>
  
  <tr>
    <td style="border:1pt solid #CCC;">
      7
    </td>
    
    <td style="border:1pt solid #CCC;">
      Manual Failover
    </td>
    
    <td style="border:1pt solid #CCC;">
      This state occurs on the principal server instance when the user fails over manually from the principal, or on the mirror server instance when a force service is executed at the mirror.
    </td>
  </tr>
  
  <tr>
    <td style="border:1pt solid #CCC;">
      8
    </td>
    
    <td style="border:1pt solid #CCC;">
      Automatic Failover
    </td>
    
    <td style="border:1pt solid #CCC;">
      This state occurs on the mirror server instance when the operating mode is high safety with automatic failover (synchronous) and the mirror and witness server instances cannot connect to the principal server instance.
    </td>
  </tr>
  
  <tr>
    <td style="border:1pt solid #CCC;">
      9
    </td>
    
    <td style="border:1pt solid #CCC;">
      Mirroring Suspended
    </td>
    
    <td style="border:1pt solid #CCC;">
      This state occurs on both partner instances when the user suspends (pauses) the mirroring session or when the mirror server instance encounters an error. It also occurs on the mirror server instance following a force service command. When the mirror comes online as the principal, mirroring is automatically suspended.
    </td>
  </tr>
  
  <tr>
    <td style="border:1pt solid #CCC;">
      10
    </td>
    
    <td style="border:1pt solid #CCC;">
      No Quorum
    </td>
    
    <td style="border:1pt solid #CCC;">
      If a witness is configured, this state occurs on the principal or mirror server instance when it cannot connect to its partner or to the witness server instance.
    </td>
  </tr>
  
  <tr>
    <td style="border:1pt solid #CCC;">
      11
    </td>
    
    <td style="border:1pt solid #CCC;">
      Synchronizing Mirror
    </td>
    
    <td style="border:1pt solid #CCC;">
      This state occurs on the mirror server instance when there is a backlog of unsent log. The status of the session is Synchronizing.
    </td>
  </tr>
  
  <tr>
    <td style="border:1pt solid #CCC;">
      12
    </td>
    
    <td style="border:1pt solid #CCC;">
      Principal Running Exposed
    </td>
    
    <td style="border:1pt solid #CCC;">
      This state occurs on the principal server instance when the operating mode is high protection (synchronous) and the principal cannot connect to the mirror server instance.
    </td>
  </tr>
  
  <tr>
    <td style="border:1pt solid #CCC;">
      13
    </td>
    
    <td style="border:1pt solid #CCC;">
      Synchronizing Principal
    </td>
    
    <td style="border:1pt solid #CCC;">
      This state occurs on the principal server instance when there is a backlog of unsent log. The status of the
    </td>
  </tr>
</table>

Now that we know the Event and the State here is how to add an Alert to notify you that the state of DB mirroring has changed.

sql
USE [msdb]
GO

/****** Object:  Alert [DBM State Change]    Script Date: 10/15/2009 08:03:20 ******/
EXEC msdb.dbo.sp_add_alert @name=N'DBM State Change', 
		@message_id=0, 
		@severity=0, 
		@enabled=1, 
		@delay_between_responses=0, 
		@include_event_description_in=1, 
		@category_name=N'[Uncategorized]', 
		@wmi_namespace=N'\.rootMicrosoftSqlServerServerEventsMSSQLSERVER', 
		@wmi_query=N'SELECT * FROM DATABASE_MIRRORING_STATE_CHANGE WHERE State = 6 ', 
		@job_id=N'00000000-0000-0000-0000-000000000000'
GO
```
This is an alert that I created on the principal server. I also have created a similar alert on the mirror server where I look for state = 5. These two alerts will notify me if the connection between the Principal and Mirror is lost due to network or some other failure.
  
To receive notification when this event happens it is simple to just create an operator and have the event email the operator if and when the event conditions are met.

What other Mirror Events should every DBA monitor? I find the unsent and unrestored log to be two very import events to receive notifications for. For those events just simply create a new event for the event ID in the table below and set you monitor threshold.

<table style="border:1pt solid #000;">
  <tr style="background-color:#CCCCCC;">
    <th style="border:1pt solid #000;">
      Database Mirroring Monitor warning
    </th>
    
    <th style="border:1pt solid #000;">
      Event name
    </th>
    
    <th style="border:1pt solid #000;">
      Event ID
    </th>
  </tr>
  
  <tr>
    <td style="border:1pt solid #CCC;">
      Warn if the unsent log exceeds the threshold
    </td>
    
    <td style="border:1pt solid #CCC;">
      Unsent log
    </td>
    
    <td style="border:1pt solid #CCC;">
      32042
    </td>
  </tr>
  
  <tr>
    <td style="border:1pt solid #CCC;">
      Warn if the unrestored log exceeds the threshold
    </td>
    
    <td style="border:1pt solid #CCC;">
      Unrestored log
    </td>
    
    <td style="border:1pt solid #CCC;">
      32043
    </td>
  </tr>
  
  <tr>
    <td style="border:1pt solid #CCC;">
      Warn if the age of the oldest unsent transaction exceeds the threshold
    </td>
    
    <td style="border:1pt solid #CCC;">
      Oldest unsent transaction
    </td>
    
    <td style="border:1pt solid #CCC;">
      32044
    </td>
  </tr>
  
  <tr>
    <td style="border:1pt solid #CCC;">
      Warn if the mirror commit overhead exceeds the threshold
    </td>
    
    <td style="border:1pt solid #CCC;">
      Mirror commit overhead
    </td>
    
    <td style="border:1pt solid #CCC;">
      32045
    </td>
  </tr>
</table>

You can also script this by using sp\_add\_alert as follows:

sql
USE [msdb]
GO

/****** Object:  Alert [DB Mirroring Unsent Log Warning]    Script Date: 10/15/2009 08:14:29 ******/
EXEC msdb.dbo.sp_add_alert @name=N'DB Mirroring Unsent Log Warning', 
		@message_id=32042, 
		@severity=0, 
		@enabled=0, 
		@delay_between_responses=0, 
		@include_event_description_in=1, 
		@category_name=N'[Uncategorized]', 
		@job_id=N'00000000-0000-0000-0000-000000000000'
GO
```
Good Luck and Happy monitoring!