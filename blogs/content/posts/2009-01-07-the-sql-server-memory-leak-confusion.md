---
title: The SQL Server Memory Leak Confusion
author: SQLDenis
type: post
date: 2009-01-07T12:47:46+00:00
ID: 276
url: /index.php/datamgmt/datadesign/the-sql-server-memory-leak-confusion/
views:
  - 18680
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - myth
  - performance tuning
  - sql server

---
Not too many moons ago when I started out with SQL Server I was convinced that there was a memory leak inside SQL Server. I ran it locally and the RAM usage just keep growing and growing until my computer was so slow that I had to restart SQL Server. This fixed things for a while but after I ran some poorly written queries it would spike up again and stay high.

There are question about this in forums/fora/newgroups all the time, people think that the sqlserver.exe process has a gigantic memory leak. This is just not true, the reason SQL Server will use as much memory as it can possible grab is that it is much more expensive to read from disk than from RAM. This is also the reason that you want as much RAM as you can afford to make SQL Server happy (and your customers). If your database is not gigantic it is possible that all your data could be in RAM.

How do you know you need more RAM?
  
Take a look at your Buffer Cache Hit Ratio, ideally you want to be at 95% plus.
  
Mine right now is 99.927 which is very good. To look at the Buffer Cache Hit Ratio you need to open up Performance in Control Panel–>Administrative Tools. Click on the + to add a new counter, from the Performance object select _SQL Server: Buffer Manager_, from the Select counters from list select _buffer cache hit ratio_
  
Take a look at the screen shot below if the text is too confusing

[<img src="http://farm4.static.flickr.com/3460/3177172358_88e1952570_o.jpg" width="381" height="377" alt="Buffer Cache Hit Ratio" />][1]

So in the end there is no memory leak, because RAM is so much faster than disk SQL Server will use all it can grab. Solid State Disks might change that in the future but for now these Solid State Disks are not large enough
  


If you are running SQL Server on your local machine make sure that you cap the memory it can use to something like 500MB or 25% less than the total memory on the machine. This will prevent it from slowing down your machine.

\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][2] forum or our [Microsoft SQL Server Admin][3] forum**<ins></ins>

 [1]: http://www.flickr.com/photos/denisgobo/3177172358/ "Buffer Cache Hit Ratio by Denis Gobo, on Flickr"
 [2]: http://forum.ltd.local/viewforum.php?f=17
 [3]: http://forum.ltd.local/viewforum.php?f=22