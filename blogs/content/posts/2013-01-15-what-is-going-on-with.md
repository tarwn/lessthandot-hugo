---
title: What is going on with Database Mail in SQL Server
author: SQLDenis
type: post
date: 2013-01-15T19:41:00+00:00
ID: 1919
excerpt: "Two days ago I wrote a post  Setting up SQL Server Database Mail with gmail, I showed you how to setup SQL Server Database Mail to work with a gmail account. In yesterday's post Database Mail maintenance in SQL Server we looked at some maintenance, toda&hellip;"
url: /index.php/datamgmt/dbprogramming/what-is-going-on-with/
views:
  - 45395
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - database mail
  - mail
  - sql server 2008
  - sql server 2012

---
Two days ago I wrote a post [Setting up SQL Server Database Mail with gmail][1], I showed you how to setup SQL Server Database Mail to work with a gmail account. In yesterday&#8217;s post [Database Mail maintenance in SQL Server][2] we looked at some maintenance, today we are going to look at how we can check what is being done by Database Mail. There are a couple of Catalog Views that we will take a look at:

sysmail\_event\_log
  
sysmail_faileditems
  
sysmail_sentitems
  
sysmail_unsentitems
  
sysmail_mailattachments 

## sysmail\_event\_log

The event_type column will have the type of message for each Windows or SQL Server message returned by the Database Mail system. The types of messages are errors, warnings, informational messages, success messages, and additional internal messages. Here is for example a query that brings back the last 100 rows that were generated

sql
SELECT TOP 100 * FROM msdb.dbo.sysmail_event_log
ORDER BY last_mod_date DESC 
```

## sysmail_faileditems

This view will hold all the messages that did not go out, sent_status will be failed in all rows

To see all the failed messages, you can just execute this simple query

sql
SELECT * FROM msdb.dbo.sysmail_faileditems
```

## sysmail_sentitems

Database Mail will mark messages as sent when they are successfully submitted to an SMTP mail server. Database Mail will mark the message as sent when it is accepted by the SMTP mail server. For E-mail errors that occur on the SMTP mail server, such as an undeliverable recipient e-mail address, errors are not returned to Database Mail. Those e-mails are recorded as sent, even though they are not delivered. This view will help you determine that something is wrong with the SMTP server

To see all the items that were sent, you can use the following query

sql
SELECT * FROM msdb.dbo.sysmail_sentitems
```

## sysmail_unsentitems

You can use the sysmail_unsentitems view when you want to see how many messages are waiting to be sent and how long they have been in the mail queue.

Messages can have the unsent status for these reasons:
  
The message is new, and has been placed on the mail queue, Database Mail is working on other messages and has not yet reached this message.
  
The Database Mail external program is not running and no mail is being sent.

The sent\_status column will be unsent if Database Mail has not attempted to send the mail. The sent\_status column will be retrying if Database Mail failed to send the message but is trying again.

Here is a simple query that will return all unsent items

sql
SELECT * FROM msdb.dbo.sysmail_unsentitems
```

## sysmail_mailattachments

You can use the sysmail_mailattachments view to quickly see who got what attachment. The view has the file name as well as the size of the attachment

Here is a simple query to get you started

sql
SELECT * FROM msdb.dbo.sysmail_mailattachments
```

Of course you can also combine these views by joining on the mailitem_id. here is just one such query

sql
SELECT * FROM msdb.dbo.sysmail_mailattachments a
JOIN msdb.dbo.sysmail_unsentitems u ON a.mailitem_id = u.mailitem_id
```

This was just a short post showing you these five catalog views that might help you with troubleshooting Database Mail. Explore the views and incorporate them in your T-SQL library.

 [1]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/setting-up-sql-server-database
 [2]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/database-mail-maintenance-in-sql