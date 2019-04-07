---
title: Setting up Kerberos authentication between SQL Servers
author: David Forck (thirster42)
type: post
date: 2012-03-07T12:58:00+00:00
ID: 1549
excerpt: 'Throughout the last couple of years I’ve constantly heard that using SA for linked servers is a horrible idea.  Setting up another SQL account with full rights to a server for the link is just about as bad an idea.  If only there was some way to pass th&hellip;'
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/setting-up-kerberos-authentication-between/
views:
  - 31438
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server Admin

---
Throughout the last couple of years I’ve constantly heard that using SA for linked servers is a horrible idea. Setting up another SQL account with full rights to a server for the link is just about as bad an idea. If only there was some way to pass the user’s authentication between servers….

Oh wait, there is! Thanks to Kerberos authentication we can set up our SQL Servers to pass authentication of a user from one place to another, and now a user will never see anything that they weren’t supposed to see!

Here are the steps I followed in order to get this to work for me.

**1. SQL Service Accounts**
  
To do this you need a couple of users to be set up as your service accounts. Generally you want a separate user for each service so that if one account is compromised/locked out not all of your instances are compromised. These accounts will need to be set up to log on as a service. You then go into configuration manager and set the accounts up for the services (this will require the services to restart).

**2. Set SPN’s**
  
The service principal name is essentially what tells the servers that they trust each other. To do this you will need domain admin privileges and to run the following commands for each server. Also make sure that the service is off when you run these.

SETSPN –A MSSQLSvc/\[server name].[domain].com [domain\]\[service account\]
  
SETSPN –A MSSQLSvc/\[server name].[domain].com:1433 [domain\]\[service account\]

So an example would look like this:
  
SETSPN –A MSSQLSvc/sql1.thirsterdomain.com thirsterdomainsql1ServiceAccount
  
SETSPN –A MSSQLSvc/sql1.thirsterdomain.com:1433 thirsterdomainsql1ServiceAccount

**3. Set up linked servers**
  
The very last thing to do is to set up the linked server on each server. So if we had SQL1 and SQL2 we’d run the following commands:

sql
--run this on sql2
exec sp_addlinkedserver @server='sql1', @srvproduct='', @provider='SQLNCLI',  @provstr='Integrated Security=SSPI'
go
exec sp_addlinkedsrvlogin 'sql2','true'

--run this on sql1
exec sp_addlinkedserver @server='sql2', @srvproduct='', @provider='SQLNCLI',  @provstr='Integrated Security=SSPI'
go
exec sp_addlinkedsrvlogin 'sql2','true'
```
If you have any questions or any issues with getting this working leave a comment.