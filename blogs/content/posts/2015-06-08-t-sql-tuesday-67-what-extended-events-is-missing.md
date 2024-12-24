---
title: 'T-SQL Tuesday #67 – What Extended Events is Missing'
author: Jes Borland
type: post
date: 2015-06-09T00:00:27+00:00
ID: 3403
url: /index.php/datamgmt/dbprogramming/t-sql-tuesday-67-what-extended-events-is-missing/
views:
  - 3031
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
[<img class="alignnone" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/olap_1.gif" alt="" width="154" height="154" />][1]<a href="/index.php/uncategorized/youre-invited-to-t-sql-tuesday-67-extended-events/" target="_blank">It's my T-SQL Tuesday!</a> I've spent the last year learning and teaching Extended Events – because if you really want to learn something, teach. XE can be used for everything from very simple tasks, such as counting the number of query executions, to very complicated tasks, such as [anything Jonathan Kehayias writes about][2]! I'm encouraging other bloggers to write about XE today in hopes that I'll learn some new stuff – and if people have questions, that we'll be able to teach them.

### It's great, but it's not complete

XE is supposed to be the replacement for Profiler. But, Profiler had two great features that XE doesn't. I've found a workaround for one, but not the other – maybe someone will be able to solve that mystery for me.

### Stopping a session

When I created a Profiler trace, I could "Enable trace stop time", and the trace would end at that particular time. I used this for many reasons – when I started a trace and knew I'd get caught up in something else, and didn't want it to run until I (hopefully) remembered to stop it; when the server would always get slow between, say, 10:00 am and 11:00 am, but no one knew why; or when looking for things that happened in the middle of my night – I didn't want to get up at 2:00 am for Profiler.

XE doesn't have this capability. It's not in the New Session Wizard, it's not in the New Session un-wizard, and it's not in the T-SQL. However, this is easy to work around. After creating a session, create a SQL Server Agent Job that stops the session, then schedule it.

Here's my session – I'm tracking database and object creation.

[<img class="aligncenter wp-image-3404 size-full" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2015/06/CreateEventSession.png" alt="CreateEventSession" width="587" height="259" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2015/06/CreateEventSession.png 587w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2015/06/CreateEventSession-300x132.png 300w" sizes="(max-width: 587px) 100vw, 587px" />][3]

I create a Job that has one step – this is to set the STATE = STOP.

[<img class="size-full wp-image-3406 aligncenter" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2015/06/CreateJobStep.png" alt="CreateJobStep" width="820" height="668" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2015/06/CreateJobStep.png 820w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2015/06/CreateJobStep-300x244.png 300w" sizes="(max-width: 820px) 100vw, 820px" />][4]

Then, I schedule the job to run once.

[<img class="size-full wp-image-3405 aligncenter" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2015/06/JobSchedule.png" alt="JobSchedule" width="706" height="634" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2015/06/JobSchedule.png 706w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2015/06/JobSchedule-300x269.png 300w" sizes="(max-width: 706px) 100vw, 706px" />][5]

I check Event Viewer and confirm the job ran. In SSMS, I can see the session is stopped.

This works, but it isn't as easy or convenient as having the option right in the session creation.

### Correlating a Session with Perfmon

Once upon a time, I learned that if I set up a Perfmon collection and a Profiler trace, then stopped them both, [I could view the results at the same time in Profiler][6], thus allowing me to see if CPU usage spiked or page life expectancy dropped, what executed at that time. I solved a lot of problems with this over the years.

Much to my dismay, there is no tool like this for Extended Events. I've wished so hard for this, I thought about writing it myself – but my programming skills are so rusty, I'd probably be working on it until XE is deprecated. Perhaps someone will read this and be inspired? I can hope!

### But I Still Love Extended Events

Despite these two flaws, I love Extended Events. The ease of setup, the breadth of events to track, the advanced filtering, the minor impact on performance – all of these add up to a better tool. I can't wait to hear what you have to write about for T-SQL Tuesday!

 [1]: /index.php/uncategorized/youre-invited-to-t-sql-tuesday-67-extended-events/
 [2]: https://www.sqlskills.com/blogs/jonathan/category/extended-events/
 [3]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2015/06/CreateEventSession.png
 [4]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2015/06/CreateJobStep.png
 [5]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2015/06/JobSchedule.png
 [6]: http://www.mssqltips.com/sqlservertip/1212/correlating-performance-monitor-and-sql-server-profiler-data/