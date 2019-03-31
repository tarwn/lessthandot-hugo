---
title: SQL Server Database owner ~~UNKNOWN~~
author: SQLDenis
type: post
date: 2013-05-14T10:02:00+00:00
excerpt: 'Today I was checking an older server and decided to run sp_helpdb. On a bunch of databases I noticed that the owner was ~~UNKNOWN~~. The only reason I noticed this was when I tried to look at a specific database which is mirrored. I was greeted with thi&hellip;'
url: /index.php/datamgmt/dbadmin/sql-server-database-owner-unknown/
views:
  - 22476
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - security
  - sql server 2000
  - sql server 2005
  - sql server 2008
  - sql server 2012

---
Today I was checking an older server and decided to run sp_helpdb. On a bunch of databases I noticed that the owner was ~~UNKNOWN~~. The only reason I noticed this was when I tried to look at a specific database which is mirrored. I was greeted with this dialog box

> Cannot show requested dialog.
> 
> Additional information:
    
> Cannot show requested dialog.(SqlMgmt)
      
> Property Owner is not available for Database'[Your Database Name]&#8217;. This property may not exist for this
      
> object, or may not be retrievable due to insufficient access rights. (Microsoft.SqlServer.Smo)

What does this mean, was the server hacked and someone changed the owner? No, what this means is that the owner of the database was a windows account which no longer exists, the person probably left and the account was removed.

All you have to do is change the owner to a valid login

If you want it to be sa or a sql login, this example is for sa, change sa to something else if you want a different sql login

<pre>ALTER AUTHORIZATION ON DATABASE::[YourDatabase] TO [sa];</pre>

If you want it to be a windows login, you can do the following

<pre>ALTER AUTHORIZATION ON DATABASE::[YourDatabase] TO [DomainLogin];</pre>

If you run sp_helpdb now, you will see that the owner has been changed

If you are old school, you can also use sp_changedbowner to make the change

<pre>USE YourDatabase
GO

EXEC sp_changedbowner 'sa'</pre>