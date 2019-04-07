---
title: Do you have Chimney Offload State disabled?
author: SQLDenis
type: post
date: 2011-03-18T11:19:00+00:00
ID: 1081
excerpt: |
  This is a short post, leave me a comment if you have TCP Chimney disabled or enabled.
  
  You can easily check by running this from a command prompt netsh int tcp show global
  
  
  Or if you want to use SSMS, you can use xp_cmdshell 
  xp_cmdshell 'netsh i&hellip;
url: /index.php/datamgmt/datadesign/do-you-have-chimney-offload/
views:
  - 19276
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
This is a short post, leave me a comment if you have TCP Chimney disabled or enabled. This was suggested to us when we had a problem with mirroring between two servers a while back. I am just wondering if it is disabled or enabled for most of you

You can easily check by running this from a command prompt _netsh int tcp show global_

Or if you want to use SSMS, you can use _xp_cmdshell_ 

sql
xp_cmdshell 'netsh int tcp show global'
```

Here is the output for one of my servers

<pre>Querying active state...
NULL
TCP Global Parameters
----------------------------------------------
Receive-Side Scaling State          : enabled 
Chimney Offload State               : disabled 
Receive Window Auto-Tuning Level    : normal 
Add-On Congestion Control Provider  : ctcp 
ECN Capability                      : disabled 
RFC 1323 Timestamps                 : disabled 
NULL
NULL</pre>

There is a post on the CSS SQL Server Engineers blog where you can read why disabling it could speed up your queries if you have a problem: http://blogs.msdn.com/b/psssql/archive/2010/02/21/tcp-offloading-again.aspx

So leave me a comment telling me if you have it disabled or enabled. And if you have it disabled, did you disable it because you had a problem?