---
title: 'T-SQL Tuesday #67 Extended Events Wrap-up'
author: Jes Borland
type: post
date: 2015-06-17T13:33:38+00:00
ID: 3413
url: /index.php/uncategorized/t-sql-tuesday-67-extended-events-target-blog/
views:
  - 4566
rp4wp_auto_linked:
  - 1
categories:
  - Uncategorized

---
<img class="alignright" src="/wp-content/uploads/blogs/DataMgmt/olap_1.gif" alt="" width="154" height="154" />Last week was a successful sixty-seventh T-SQL Tuesday! I asked bloggers to tell me why they loved Extended Events, why they hated it, what their favorite tricks were, or what they had recently learned. I learned several new tips and tricks! Here's a recap of the blogs. Huge thanks to all the bloggers who took time out of their week to help me – and you! – learn.

<ol type="1">
  <li value="1">
    Andrew McDermid – <a href="http://andrewmcdermid.net/tsql-tuesday-67-why-extended-and-why-events/" target="_blank">“Why extended? And why events?”</a> – Andrew gives an overview of the Extended Events framework. He also talks about the difference between polling and tracing, which I think is a great point in favor of XE.
  </li>
  <li>
    Rob Farley – “<a href="http://sqlblog.com/blogs/rob_farley/archive/2015/06/09/dbas-and-the-internet-of-things.aspx" target="_blank">DBAs and the Internet of Things</a>” – “<span style="color: #000000">Imagine that each configured event is a smart device, sitting somewhere, producing data in some semi-structured form.” Rob tells us why he thinks DBAs should be using XE to help them be superheros in an IoT world. </span>
  </li>
  <li>
    Boris Hristov – “<a href="http://borishristov.com/blog/t-sql-tuesday-67-extended-events-and-distributed-replay/" target="_blank">Extended Events and... Distributed Replay</a>?” – Distributed Replay is one of those tools, like the first iterations of XE, that I don't use enough because it's not intuitive – and there's no GUI to get started with. Boris loves it, though. Here, he walks us through how he solved the problem of capturing a workload with XE, then converted it to a trace that DR could read. This is great stuff! I love that he walks us through his troubleshooting approach.
  </li>
  <li>
    Wayne Sheffield – “<a href="http://www.sqlsolutionsgroup.com/wean-off-sql-profiler-part-1/" target="_blank">Weaning yourself off of SQL Profiler (Part 1)</a>” – Wayne breaks down a problem many of us have – we've been using SQL Server for so long that a lot of our default troubleshooting tools use...Profiler. How do you move from Profiler to XE? Read his blog! (And...part 1? I can't wait for the series, Wayne!)
  </li>
  <li>
    Aaron Bertrand – “<a href="http://sqlperformance.com/2015/06/extended-events/t-sql-tuesday-67-backup-restore" target="_blank">New Backup and Restore Extended Events</a>” – Backups and restores are the bread and butter of being a <del>good</del> great DBA. Aaron found some new XE events for backups and restores in SQL Server 2016 CTP2. I can't wait to try these out!
  </li>
  <li>
    Kenneth Fisher – “<a href="//sqlstudies.com/2015/06/09/a-walk-through-of-creating-the-activity-tracking-template-using-extended-events/" target="_blank">A walk-through of creating the Activity Tracking template using Extended Events</a>” – Kenneth wanted to capture query activity in a specific database, just like he could with Profiler. He walks us through setting up the same session in the XE wizard.
  </li>
  <li>
    Joseph Harkins – “<a href="http://www.synchrotronics.net/blog/t-sql-tuesday-67-extended-events/" target="_blank">T-SQL TUESDAY #67 EXTENDED EVENTS</a>” – First off, let me congratulate Joseph on starting his blog. It's one of the best ways to learn and share! In his first blog, he dives into how XE can be used to validate that an Availability Group secondary set up for read-only routing is indeed receiving read-only requests. Excellent work!
  </li>
  <li>
    I wrote about two features I feel XE is missing in “<a href="/index.php/datamgmt/dbprogramming/t-sql-tuesday-67-what-extended-events-is-missing/" target="_blank">What Extended Events is Missing</a>“. One has a workaround; the other doesn't.
  </li>
  <li>
    Steve Jones – “<a href="https://voiceofthedba.wordpress.com/2015/06/09/t-sql-tuesday-67-extended-events-for-dbcc/" target="_blank">Extended Events for DBCC</a>” – Steve shows us how to create an XE session to track when DBCC events are run – a great tool for DBAs!
  </li>
  <li>
    Mike Fal – “<a href="http://www.mikefal.net/2015/06/09/tsql2sday-powershell-and-extended-events/" target="_blank">#Powershell and Extended Events</a>” – I know Mike, and I know he loves PowerShell, but doesn't love XE. I'm delighted he contributed to my T-SQL Tuesday on XE regardless! Mike digs into XE with POSH, showing how it can be used to get information about sessions.
  </li>
  <li>
    Vicky Harp – “<a href="http://community.idera.com/blog/idera/t-sql-tuesday-67-introduction-to-idera-sql-xevent-profiler/" target="_blank">Introduction to Idera SQL XEvent Profiler</a>” – Do you want to set up an XE session, but use a tool that looks like Profiler? It's your lucky day! Idera has a <em>free</em> tool that lets you do just that! (Full disclosure: Vicky works for Idera. When she mentioned writing this blog on Twitter, I wholly supported the idea. If a vendor tools helps you get started with XE, I'm all for it!)
  </li>
  <li>
    Paul Randal – “<a href="http://www.sqlskills.com/blogs/paul/t-sql-tuesday-67-monitoring-log-activity-with-extended-events/" target="_blank">Monitoring log activity with Extended Events</a>” – If you want to learn about the transaction log, Paul is one of the best people to learn from. Here, he shows us that when a transaction commits, there is a write to the log file – using XE.
  </li>
</ol>

<ol type="1">
  <li value="13">
    John Morehouse – “<a href="http://sqlrus.com/2015/06/t-sql-tuesday-67-extended-events/" target="_blank">Extended Events</a>” – John wanted to reduce the number of SQL logins being used, in favor of Windows login. My favorite part is that he uses the histogram target for the results!
  </li>
  <li>
    Mark Wilkinson – “<a href="http://m82labs.com/deadlock-graph-posh/" target="_blank">Retrieving Deadlock Graphs with PowerShell</a>” – XE, POSH, and deadlocks? Be still, my beating nerd heart! Mark shows how to use the system_health session to view any deadlocks graphs that have been captured, then extends (get it?!) that to PowerShell. Awesome stuff!
  </li>
  <li>
    Jason Brimhall – “<a href="http://bit.ly/XETreehugger" target="_blank">Extended Events, Birkenstocks and SQL Server</a>” –  Jason wins Most Creative Blog Title this month! If your SQL Servers are set to tree-hugger mode – Power Saving – they are not pushing out the CPU cycles you need. How can you tell? XE to the rescue! I loved this blog post.
  </li>
  <li>
    Chris Yates... TBD. Yep, Chris, you said you were going to write a post, and I'm eagerly waiting to read it!
  </li>
</ol>

What a great set of blogs to read! I hope this keeps you busy reading and learning for a couple of weeks...just in time to catch the July 2015 edition of T-SQL Tuesday!