---
title: Setting trust between 2 windows server 2003 domains results in “The specified user already exist”
author: Christiaan Baes (chrissie1)
type: post
date: 2009-10-21T05:45:29+00:00
ID: 593
url: /index.php/sysadmins/os/windows/setting-trust-between-2-windows-server-2/
views:
  - 6084
categories:
  - 2003 Server
  - Windows
tags:
  - trust
  - windows server 2003

---
So yesterday we were setting up a trust between my domain and another domain. Both were Windows Server 2003. 

In prinicipal this is a very easy task but sometimes you get the most interesting error messages.

Like this one.

> The specified user already exists

After googling for a while we ended up with no real answer. A lot of maybes and pointers to search in a certain direction. 

In the end we found the problem there was a computer on the other domain that had the same NetBIOS name than my domain. Removing that computer (user) from the domain was enough to make it continue the wizard and have the trust up and running 2 minutes later. 

Finding the culprit was a bit more difficult than first anticipated. So instead of a 10 minute install we spent an hour to find the problem.
  
And solved a few more along the way ;-).