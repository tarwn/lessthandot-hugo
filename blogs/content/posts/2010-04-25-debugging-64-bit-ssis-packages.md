---
title: Debugging 64 bit SSIS packages
author: SQLDenis
type: post
date: 2010-04-25T17:44:00+00:00
ID: 768
url: /index.php/datamgmt/dbprogramming/debugging-64-bit-ssis-packages/
views:
  - 6260
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
tags:
  - debugging
  - howto
  - ssis
  - yip

---
If you ever try to debug a script task by setting a breakpoint and the package is on a 64 bit machine it will just ignpore the breakpoint.
  
I ran into this problem myself a while ago and this week a co-worker also ran into it and asked me how to resolve it.

It is pretty simple, all you have to do is click on _Project_, then select _Debug Properties_. Under _Configuration Properties_, click on _Debugging_. Make _Run64BitRuntime_ _False_. See image below. 

<img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt//debug2.png" alt="" title="" width="696" height="422" />

Now start the debugger again, you will see now that it will stop at the breakpoint.

I also captured it in a video and uploaded it on YouTube, there is even a HD 720p format
  
{{< youtube id="VJrshV22MD4" autoplay="false" >}}

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][2] forum**<ins></ins>

 [1]: http://forum.lessthandot.com/viewforum.php?f=17
 [2]: http://forum.lessthandot.com/viewforum.php?f=22