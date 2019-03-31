---
title: 'Get local lab and instances going.  Replication / SQL Agent'
author: Ted Krueger (onpnt)
type: post
date: 2009-05-29T20:32:24+00:00
url: /index.php/datamgmt/datadesign/get-local-lab-and-instances-going-replic/
views:
  - 6456
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
If you are anything like me, you have about 4 or 5 instances installed on your laptop. Needless to say this can be a pain in the rear. Today I was blogging on replication and affects on identity seeds and wanted to use my default instance installed locally to test everything in order to provide the exact scenario to my readers. Well, that didn&#8217;t go so well.

First problem. I couldn&#8217;t configure the distributor 

error: &#8220;SQL Server is unable to connect to the server &#8216;LKFW00TKLKF00TKSQL05&#8217;. Additional Information SQL Server replication requires the actual server name to make a
  
connection to the server. Connections through a server alias, IP address, or any
  
other alternate name are not supported. Specify the actual server name, &#8216;LKFW00TKLKF00TKSQL05&#8217;. (Replication.Utilities)&#8221;

I knew exactly how to fix that sense I&#8217;ve had to so many times when I do multiple local installs. Simply run the following to clear up the server names

<pre>USE MASTER
GO
SP_DROPSERVER 'LKFW00TKLKF00TKSQL05'
GO
USE MASTER
GO
SP_ADDSERVER 'LKFW00TK', 'LOCAL'
GO</pre>

Restart SQL Server and you&#8217;ll get in at least.

Second issue. SQL Agent wasn&#8217;t running mostly because I don&#8217;t let it so my laptop actually runs and isn&#8217;t bogged down. So I start it&#8230;
  
Error: &#8220;SQLServerAgent could not be started (reason: SQLServerAgent must be able to connect to SQLServer as SysAdmin, but &#8216;(Unknown)&#8217; is not a member of the SysAdmin role). &#8221;

Urgh! Alright. I checked the cached credentials.
  
e.g.

<pre>select * from msdb..syscachedcredentials</pre>

OK, I&#8217;m the only one. That&#8217;s good. Thinking&#8230;.

OK, for the record. If you get this error and you have way too many instances installed, go to services.msc first and make sure you are running the SQL Server services and the SQL Server Agent under the same account. This is the quick fix 🙂 Either that or ensure the account is a local administrator if you have BUILTINAdministrators in the sysadmin role. Now back to the other blog which caused me so many headaches.