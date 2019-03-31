---
title: Database Mail maintenance in SQL Server
author: SQLDenis
type: post
date: 2013-01-14T18:05:00+00:00
excerpt: "In yesterday's post Setting up SQL Server Database Mail with gmail, I showed you how to setup SQL Server Database Mail to work with a gmail account, today we are going to look at doing some Database Mail maintenance."
url: /index.php/datamgmt/dbprogramming/database-mail-maintenance-in-sql/
views:
  - 8227
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
In yesterday&#8217;s post [Setting up SQL Server Database Mail with gmail][1], I showed you how to setup SQL Server Database Mail to work with a gmail account, today we are going to look at doing some Database Mail maintenance. 

Database Mail messages and their attachments are stored in the msdb database. If you don&#8217;t periodically delete these messages then the msdb database might growe larger than expected. 

There are two stored procedure that you can call to delete items related to mail in the msdb database. The first stored procedure we are look at is sysmail\_delete\_mailitems\_sp. The sysmail\_delete\_mailitems\_sp stored procedure can be called with two optional arguments

> [ **@sent_before**= ] &#8216;sent_before&#8217;
  
> Deletes e-mails up to the date and time provided as the sent\_before argument. sent\_before is datetime with NULL as default. NULL indicates all dates.
> 
> [ **@sent_status**= ] &#8216;sent_status&#8217;
  
> Deletes e-mails of the type specified by sent\_status. sent\_status is varchar(8) with no default. Valid entries are sent, unsent, retrying, and failed. NULL indicates all statuses.

If I want to delete everything older than 2 days, I can use the following proc call

<pre>DECLARE @Date datetime
SELECT @Date = DateAdd(dd,-2,Getdate())

EXECUTE msdb.dbo.sysmail_delete_mailitems_sp @sent_before = @Date </pre>

If I want to delete all the items that have failed I can call the proc like this

<pre>EXECUTE msdb.dbo.sysmail_delete_mailitems_sp  @sent_status = 'failed' </pre>

Of course we can also combine the arguments, this will wipe out all the failed messages older than 2 days

<pre>DECLARE @Date datetime
SELECT @Date = DateAdd(dd,-2,Getdate())
SELECT @Date

EXECUTE msdb.dbo.sysmail_delete_mailitems_sp @sent_before = @Date , @sent_status = 'failed' </pre>

* * *The next proc we will look at is sysmail\_delete\_log\_sp. You can use the sysmail\_delete\_log\_sp stored procedure to permanently delete entries from the Database Mail log. This proc also has two optional arguments</p> 

> [ **@logged_before** = ] &#8216;logged_before&#8217;
  
> Deletes entries up to the date and time specified by the logged\_before argument. logged\_before is datetime with NULL as default. NULL indicates all dates.
> 
> [ **@event_type** = ] &#8216;event_type&#8217;
  
> Deletes log entries of the type specified as the event\_type. event\_type is varchar(15) with no default. Valid entries are success, warning, error, and informational. NULL indicates all event types.

Here is how we will delete all events in the log older than two days

<pre>DECLARE @Date datetime
SELECT @Date = DateAdd(dd,-2,Getdate())


EXECUTE msdb.dbo.sysmail_delete_log_sp @logged_before = @Date </pre>

Here we are deleting all the events with an event type of success

<pre>EXECUTE msdb.dbo.sysmail_delete_log_sp   @event_type = 'success'</pre>

And just like before, we can combine the two proc calls and wipe out everything with an event type of success and older than two days

<pre>DECLARE @Date datetime
SELECT @Date = DateAdd(dd,-2,Getdate())


EXECUTE msdb.dbo.sysmail_delete_log_sp @logged_before = @Date,@event_type = 'success'</pre>

I have a job running in SQL Agent, this job runs at 4 AM everyday and runs these two stored procedures to keep my msdb database from growing out of control. Just keep this in mind if you are staring out with Database Mail

 [1]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/setting-up-sql-server-database