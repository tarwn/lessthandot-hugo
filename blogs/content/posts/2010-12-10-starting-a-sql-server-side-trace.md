---
title: 'SQL Server: Starting a Server-Side Trace'
author: Jes Borland
type: post
date: 2010-12-10T14:17:42+00:00
ID: 972
excerpt: One of the many performance tuning and troubleshooting tools in the SQL Server DBA toolbelt is Profiler. You pick events you want to see, set up a trace, and watch the events roll by. The problem is that this graphical tool, especially when run from a workstation, can place a heavy load on the server.
url: /index.php/webdev/webdesigngraphicsstyling/starting-a-sql-server-side-trace/
views:
  - 10948
rp4wp_auto_linked:
  - 1
categories:
  - Web Design, Graphics and Styling
tags:
  - sql server 2008
  - sql server profiler

---
Stop me if you've heard this one…a programmer walks up to a DBA and says, “The database is slow.” 

One of the many performance tuning and troubleshooting tools in the SQL Server DBA toolbelt is Profiler. You pick events you want to see, set up a trace, and watch the events roll by. The problem is that this graphical tool, especially when run from a workstation, can place a heavy load on the server. 

A better way to perform a trace of events is to run it on the server. I was asked to do this at work last week, and had to say, “I don't know how to do that.” I learned something new, and I want to share it with you! 

**Setting Up the Trace** 

First, set up the trace using SQL Server Profiler, preferably against a development or test database. (The GUI tool can negatively affect performance, so be careful on production databases.) To do this, in SSMS go to Tools > SQL Server Profiler. Add a trace name, choose to save to a file, and enable file rollover. This last option means that when the max file size is reached, a new file will be created. 

<div class="image_block">
  <img src="/wp-content/uploads/users/grrlgeek/ProfilerPropertiesGeneral.JPG" alt="" title="" width="886" height="641" />
</div>

Next, go to Events Selection and check your events. I’ve chosen a standard template for this test. 

<div class="image_block">
  <img src="/wp-content/uploads/users/grrlgeek/ProfilerPropertiesEvents.JPG" alt="" title="" width="888" height="641" />
</div>

Click “Run” to begin the trace. I executed a stored procedure in my AdventureWorks database to make sure the trace was capturing data. 

<div class="image_block">
  <img src="/wp-content/uploads/users/grrlgeek/ProfilerRunning.JPG" alt="" title="" width="692" height="565" />
</div>

**Making a Server-Side Trace** 

Now, I stop the trace and create the script for a server-side trace. In Profiler, go to File > Export > Script Trace Definition > For SQL Server 2005 – 2008 R2. You will be prompted to save the .sql file, and notified when it is saved. 

Now, open the .sql file in SQL Server Management Studio. It will look similar to this: 

<div class="image_block">
  <img src="/wp-content/uploads/users/grrlgeek/ProfilerTraceSQL.JPG" alt="" title="" width="786" height="520" />
</div>

On line 19, you will need to specify where to save the output files. This might be a location on the server, or a share drive on your network. 

Once those settings have been chosen, verify that you are connected to the correct server and Execute. Ta-da! You’ve just started a server-side trace. 

**Stopping the Server-Side Trace** 

Eventually, you will probably want to stop this trace. Collecting an infinite amount of data may sound fun, but there won't be any value in it. 

First, you’ll need the trace ID. 

sql
SELECT * FROM :: fn_trace_getinfo(default)
```

Your results might look something like this: 

<div class="image_block">
  <img src="/wp-content/uploads/users/grrlgeek/fn_trace_getinforesults.JPG" alt="" title="" width="428" height="236" />
</div>

Traceid is the ID on the server. I currently have two, 1 and 2. Property is the ID of the trace property.
  
• 1 – Trace Options
  
• 2 – File Name
  
• 3 – Max size
  
• 4 – Stop time (if set)
  
• 5 – Current trace status. 

The status options are:
  
• 0 – Stopped
  
• 1 – Running 

I determine that the trace I just started has the ID of 2. To stop the trace, I use sp\_trace\_setstatus. The syntax is 

sql
sp_trace_setstatus traceid, statusid
```

The status options are:
  
• 0 – Stop
  
• 1 – Start
  
• 2 – Close and delete definition from server 

So, to stop traceid 2, I run this command: 

sql
sp_trace_setstatus 2, 0
```

I can verify it has been stopped by re-running 

sql
SELECT * FROM :: fn_trace_getinfo(default) 
```

I can then open the .trc file in SQL Server Profiler and examine it for performance issues. 

I was pretty excited to learn this. I hope this can help you out too!