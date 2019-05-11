---
title: 'Get local lab and instances going.  Replication / SQL Agent'
author: Ted Krueger (onpnt)
type: post
date: 2009-05-29T20:32:24+00:00
ID: 451
url: /index.php/datamgmt/datadesign/get-local-lab-and-instances-going-replic/
views:
  - 6456
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
If you are anything like me, you have about 4 or 5 instances installed on your laptop. Needless to say this can be a pain in the rear. Today I was blogging on replication and affects on identity seeds and wanted to use my default instance installed locally to test everything in order to provide the exact scenario to my readers. Well, that didn't go so well.

First problem. I couldn't configure the distributor 

error: "SQL Server is unable to connect to the server 'LKFW00TKLKF00TKSQL05'. Additional Information SQL Server replication requires the actual server name to make a
  
connection to the server. Connections through a server alias, IP address, or any
  
other alternate name are not supported. Specify the actual server name, 'LKFW00TKLKF00TKSQL05'. (Replication.Utilities)"

I knew exactly how to fix that sense I've had to so many times when I do multiple local installs. Simply run the following to clear up the server names

```sql
USE MASTER
GO
SP_DROPSERVER 'LKFW00TKLKF00TKSQL05'
GO
USE MASTER
GO
SP_ADDSERVER 'LKFW00TK', 'LOCAL'
GO
```
Restart SQL Server and you'll get in at least.

Second issue. SQL Agent wasn't running mostly because I don't let it so my laptop actually runs and isn't bogged down. So I start it...
  
Error: "SQLServerAgent could not be started (reason: SQLServerAgent must be able to connect to SQLServer as SysAdmin, but '(Unknown)' is not a member of the SysAdmin role). "

Urgh! Alright. I checked the cached credentials.
  
e.g.

```sql
select * from msdb..syscachedcredentials
```
OK, I'm the only one. That's good. Thinking....

OK, for the record. If you get this error and you have way too many instances installed, go to services.msc first and make sure you are running the SQL Server services and the SQL Server Agent under the same account. This is the quick fix 🙂 Either that or ensure the account is a local administrator if you have BUILTINAdministrators in the sysadmin role. Now back to the other blog which caused me so many headaches.