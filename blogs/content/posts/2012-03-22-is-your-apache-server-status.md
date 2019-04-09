---
title: Is your Apache Server Status wide open for the world to see?
author: SQLDenis
type: post
date: 2012-03-22T22:34:00+00:00
ID: 1572
excerpt: |
  The Apache web server comes with something called Apache Module mod_status installed
  
  From the Apache site: http://httpd.apache.org/docs/2.0/mod/mod_status.html
  The Status module allows a server administrator to find out how well their server is perf&hellip;
url: /index.php/sysadmins/os/windows/2003server/is-your-apache-server-status/
views:
  - 10996
rp4wp_auto_linked:
  - 1
categories:
  - 2003 Server
tags:
  - apache
  - security

---
The Apache web server comes with something called Apache Module mod_status installed

From the Apache site: http://httpd.apache.org/docs/2.0/mod/mod_status.html

> The Status module allows a server administrator to find out how well their server is performing. A HTML page is presented that gives the current server statistics in an easily readable form. If required this page can be made to automatically refresh (given a compatible browser). Another page gives a simple machine-readable list of the current server state.
> 
> The details given are:
> 
> The number of worker serving requests
  
> The number of idle worker
  
> The status of each worker, the number of requests that worker has performed and the total number of bytes served by the worker (*)
  
> A total number of accesses and byte count served (*)
  
> The time the server was started/restarted and the time it has been running for
  
> Averages giving the number of requests per second, the number of bytes served per second and the average number of bytes per request (*)
  
> The current percentage CPU used by each worker and in total by Apache (*)
  
> The current hosts and requests being processed (*)

If you go to a site that uses the Apache web server, you can get interesting info if you add server-status after the domain name

If you go to http://apache.org/server-status, you will get the following

Server Version: Apache/2.4.1 (Unix) OpenSSL/1.0.0g
  
Server Built: Mar 3 2012 19:35:24
  
Current Time: Friday, 23-Mar-2012 00:20:33 UTC
  
Restart Time: Tuesday, 20-Mar-2012 16:36:59 UTC
  
Parent Server Config. Generation: 4
  
Parent Server MPM Generation: 3
  
Server uptime: 2 days 7 hours 43 minutes 34 seconds
  
Total accesses: 41598844 &#8211; Total Traffic: 571.4 GB
  
CPU Usage: u1734.41 s1457.05 cu0 cs0 &#8211; 1.59% CPU load
  
207 requests/sec &#8211; 2.9 MB/second &#8211; 14.4 kB/request
  
24 requests currently being processed, 616 idle workers

This will be followed by a list of IP addresses and pages that were served.

This can potentially be dangerous, it doesn't long to write a program that will crawl the web and checking if it gets anything back when hitting domains with server-status appended

Several high traffic site have this enabled, just hit http://www.washingtonpost.com/ or http://boston.com/ and add server-status

What do you think, is this a problem or meh? I think it could be a problem, attackers now can now if you have an unpatched version of Apache running and they can take advantage of that.

**Edit**
  
And it seems that washingtonpost.com disabled it. As of this morning you see this instead

_Forbidden</p> 

You don't have permission to access /server-status on this server.

Apache Server at failover.washingtonpost.com Port 80</em>