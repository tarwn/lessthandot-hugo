---
title: Setting up SQL Server Database Mail with gmail
author: SQLDenis
type: post
date: 2013-01-13T11:24:00+00:00
ID: 1912
excerpt: "If you want to mess around with Database Mail from your laptop and you don't have a mail server, you can configure gmail for that purpose. It is pretty easy to setup, I will show you the screen from the wizard as well how to do it in T-SQL. I prefer T-SQL because I can run the same setup now on multiple machines"
url: /index.php/datamgmt/dbprogramming/setting-up-sql-server-database/
views:
  - 43827
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - database mail
  - gmail
  - sql server 2005
  - sql server 2008
  - sql server 2012

---
If you want to mess around with Database Mail from your laptop and you don't have a mail server, you can configure gmail for that purpose. It is pretty easy to setup, I will show you the screen from the wizard as well how to do it in T-SQL. I prefer T-SQL because I can run the same setup now on multiple machines

Below is the whole thing in one easy to run script
  
Make sure to change Your.Account@gmail.com to what your gmail account is, also notice that @enable_ssl =1 and that we are using port 587 (@port = '587')

```sql
IF NOT EXISTS(SELECT * FROM msdb.dbo.sysmail_profile WHERE  name = 'GmailDBMail') 
  BEGIN
    
    EXECUTE msdb.dbo.sysmail_add_profile_sp
      @profile_name = 'GmailDBMail',
      @description  = '';
  END --IF EXISTS profile
  
  IF NOT EXISTS(SELECT * FROM msdb.dbo.sysmail_account WHERE  name = 'GmailDBMail')
  BEGIN
    
    EXECUTE msdb.dbo.sysmail_add_account_sp
    @account_name            = 'GmailDBMail',
    @email_address           = 'Your.Account@gmail.com', -- <-- change this
    @display_name            = 'GmailDBMail',
    @replyto_address         = 'Your.Account@gmail.com', -- <-- change this
    @description             = '',
    @mailserver_name         = 'smtp.gmail.com',
    @mailserver_type         = 'SMTP',
    @port                    = '587',
    @username                = 'Your.Account@gmail.com', -- <-- change this
    @password                = 'yourpassword', -- <-- change this
    @use_default_credentials =  0 ,
    @enable_ssl              =  1 ;
  END --IF EXISTS  account
  
IF NOT EXISTS(SELECT *
              FROM msdb.dbo.sysmail_profileaccount pa
                INNER JOIN msdb.dbo.sysmail_profile p ON pa.profile_id = p.profile_id
                INNER JOIN msdb.dbo.sysmail_account a ON pa.account_id = a.account_id  
              WHERE p.name = 'GmailDBMail'
                AND a.name = 'GmailDBMail') 
  BEGIN
    
    EXECUTE msdb.dbo.sysmail_add_profileaccount_sp
      @profile_name = 'GmailDBMail',
      @account_name = 'GmailDBMail',
      @sequence_number = 1 ;
  END 
```

If you like to use the wizard, you can do that as well, here is what it would look like 

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/Oracle/gmailDBMail.PNG?mtime=1358082799"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/Oracle/gmailDBMail.PNG?mtime=1358082799" width="678" height="569" /></a>
</div>

Make sure to check SSL and that the port number is 587

Now that it is all done, let's send a test email

You might have to restart SQL Agent for Database Mail to start working, so do that first.

Now execute the following stored proc, change your.account@gmail.com to where you want to send it

```sql
EXEC msdb.dbo.sp_send_dbmail
    @profile_name = 'GmailDBMail',
    @recipients = 'your.account@gmail.com',
    @body = 'The test finished successfully.',
    @subject = 'Testing gmail with dbmail' ;
```
Go and check that email inbox, did you get the email? If you did not get the email, make sure to check the Database Mail Log, it will contain messages telling you what the error is. Right click on Database Mail and select View Database Mail Log

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/Oracle/DBMailLog.PNG?mtime=1358083272"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/Oracle/DBMailLog.PNG?mtime=1358083272" width="291" height="155" /></a>
</div>

A message might be the following, this is because the port was wrong
  
_The mail could not be sent to the recipients because of the mail server failure. (Sending Mail using Account 1 (2013-01-13T08:04:38). Exception Message: Cannot send mails to mail server. (The operation has timed out.)._

That is all there is to setting up Database mail with gmail