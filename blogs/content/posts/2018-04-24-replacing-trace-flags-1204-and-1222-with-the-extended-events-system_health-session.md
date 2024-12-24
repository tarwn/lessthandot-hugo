---
title: Replacing Trace Flags 1204 and 1222 with the Extended Events system_health Session
author: Jes Borland
type: post
date: 2018-04-24T16:51:47+00:00
ID: 9226
url: /index.php/datamgmt/dbprogramming/replacing-trace-flags-1204-and-1222-with-the-extended-events-system_health-session/
views:
  - 4373
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
Deadlocks in a SQL Server instance are problematic. They can cause application errors, slow performance, and unhappy users. As a DBA or developer, it's very helpful to be able to find deadlocks, review what caused them, and fix it permanently, if possible.

How do you find deadlocks? Over the years, there have been various methods, depending on what tools were available in SQL Server. Many of us used to run a Profiler or server trace to capture the Deadlock Graph event – useful if we knew when they were occurring (or they occurred all the time). We could also enable trace flags 1204 and/or 1222 to write the information to the event log – better if we knew there were issues, but they weren't predictable.

However, I don't like a messy event log. I like it neat and clean, so I can see errors easily. For example, I enable TF 3226 to suppress "Log was backed up" messages. With SQL Server 2012+, I also prefer to use the Extended Events default system_health session to view deadlock graphs – with no extra work required!

Let me walk through what a deadlock looks like with TF 1222 and compare that to the XE session.

In my instance, I have TF 1222 enabled.

[<img class="aligncenter size-full wp-image-9227" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/04/config-mgr.png" alt="config-mgr" width="414" height="496" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/04/config-mgr.png 414w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/04/config-mgr-250x300.png 250w" sizes="(max-width: 414px) 100vw, 414px" />][1]

I have simple code to create a deadlock. You can use this as an example in a test environment to replicate it.

<pre style="padding-left: 30px">/* Create deadlock - query 1 */

USE tempdb;
GO

CREATE TABLE tbl1 (id INT NOT NULL PRIMARY KEY CLUSTERED,
       col INT)
CREATE TABLE tbl2 (id INT NOT NULL PRIMARY KEY CLUSTERED,
       col INT REFERENCES tbl1(id))

BEGIN TRANSACTION
INSERT INTO tbl1 VALUES (2, 999);

/* Now, open a second query and paste this */
USE tempdb;
GO
BEGIN TRAN
INSERT INTO tbl2 VALUES (111, 2); 

/* Come back here and execute this */
INSERT INTO tbl2 VALUES (111, 555);
COMMIT TRAN</pre>

When I run the last statement, I receive an error that one of the processes was the deadlock victim.

[<img class="aligncenter size-full wp-image-9228" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/04/deadlock-error.png" alt="deadlock-error" width="609" height="100" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/04/deadlock-error.png 609w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/04/deadlock-error-300x49.png 300w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/04/deadlock-error-600x99.png 600w" sizes="(max-width: 609px) 100vw, 609px" />][2]

If I open the error log, I can see the details of the deadlock. Every bit of information is on a separate line.

[<img class="aligncenter size-full wp-image-9229" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/04/deadlock-error-log.png" alt="deadlock-error-log" width="726" height="636" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/04/deadlock-error-log.png 726w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/04/deadlock-error-log-300x263.png 300w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/04/deadlock-error-log-600x526.png 600w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/04/deadlock-error-log-342x300.png 342w" sizes="(max-width: 726px) 100vw, 726px" />][3]

This one isn't bad, but imagine a multi-statement deadlock, or a server with several deadlocks in an hour – how do you easily see if there were other errors on the server at the same time?

With SQL Server 2012+, we have a better tool to see when deadlocks occur – and the deadlock graphs are saved by default, so we don't have to read the text version to figure it out, or run a separate trace to capture them.

In SSMS, open Object Explorer and navigate to Extended Events > Sessions > system\_health > package0.event\_file. Double-click to view the data.

[<img class="aligncenter size-full wp-image-9230" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/04/xe-sessions.png" alt="xe-sessions" width="239" height="129" />][4]

I go right to Filters to find the xml\_deadlock\_report events.

[<img class="aligncenter size-full wp-image-9231" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/04/xe-filter.png" alt="xe-filter" width="627" height="236" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/04/xe-filter.png 627w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/04/xe-filter-300x113.png 300w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/04/xe-filter-600x226.png 600w" sizes="(max-width: 627px) 100vw, 627px" />][5]

[<img class="aligncenter size-full wp-image-9232" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/04/xe-filter-screen.png" alt="xe-filter-screen" width="637" height="411" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/04/xe-filter-screen.png 637w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/04/xe-filter-screen-300x194.png 300w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/04/xe-filter-screen-600x387.png 600w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/04/xe-filter-screen-465x300.png 465w" sizes="(max-width: 637px) 100vw, 637px" />][6]

Here you'll see deadlocks that have occurred. The Value field will show the XML values that you also see in the log. You can double-click on the Value field to bring up the XML.

[<img class="aligncenter size-full wp-image-9233" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/04/details.png" alt="details" width="515" height="238" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/04/details.png 515w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/04/details-300x139.png 300w" sizes="(max-width: 515px) 100vw, 515px" />][7]

Don't ignore that sneaky "Deadlock" tab, however – that's where you'll find the easier-to-read deadlock graph!

[<img class="aligncenter size-large wp-image-9234" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/04/deadlock-1024x433.png" alt="deadlock" width="1024" height="433" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/04/deadlock-1024x433.png 1024w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/04/deadlock-300x127.png 300w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/04/deadlock-768x324.png 768w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/04/deadlock-600x253.png 600w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/04/deadlock-710x300.png 710w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/04/deadlock.png 1044w" sizes="(max-width: 1024px) 100vw, 1024px" />][8]

A good description of deadlock graphs, and how to interpret them, can be found at <https://www.sqlshack.com/understanding-graphical-representation-sql-server-deadlock-graph/>.

As you modernize your data platform, you should update your troubleshooting methods and tools as well. This is an easy example of taking advantage of Extended Events to solve an old problem!

 [1]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/04/config-mgr.png
 [2]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/04/deadlock-error.png
 [3]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/04/deadlock-error-log.png
 [4]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/04/xe-sessions.png
 [5]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/04/xe-filter.png
 [6]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/04/xe-filter-screen.png
 [7]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/04/details.png
 [8]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/04/deadlock.png