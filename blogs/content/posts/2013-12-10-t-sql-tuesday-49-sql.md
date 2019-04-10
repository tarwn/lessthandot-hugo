---
title: 'T-SQL Tuesday #49: SQL Server, Waits, and You'
author: Jes Borland
type: post
date: 2013-12-10T16:01:00+00:00
ID: 2205
excerpt: "It's the monthly blog challenge known as T-SQL Tuesday! Edition 49 is being hosted by Robert Davis, and he wants us to wait around. No, that's not right - he wants us to write about waiting. Yeah, that's it."
url: /index.php/webdev/business-intelligence/t-sql-tuesday-49-sql/
views:
  - 14180
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence

---
It's the monthly blog challenge known as T-SQL Tuesday! <a href="http://www.sqlsoldier.com/wp/sqlserver/tsqltuesday49topiciswaitforit" target="_blank">Edition 49 is being hosted by Robert Davis</a>, and he wants us to wait around. No, that's not right – he wants us to write about waiting. Yeah, that's it. <img style="float: right;" src="/wp-content/uploads/users/grrlgeek/TSQL2sDay150x150.jpg?mtime=1365451350" alt="" width="150" height="150" />

You’ve read a lot about wait statistics. You know your servers have wait statistics on them. You know your applications and queries are waiting on something. Before you panic, before you go changing maxdop or rewriting queries, remember one thing: every system is going to have waits recorded. It’s the nature of computing. The chance that every resource a query needs is available at the exact millisecond it needs it is nonexistent. It takes time to read data from storage to memory. It takes time to write data to storage. It takes time to send results across the network to the client.

As always, the most important thing you can do is have a baseline of wait statistics on your servers. What is normal for each server? One server may normally have high PAGEIOLATCH waits because the software does large reads; another may have high WRITELOG waits because the software uses cursors.

(Want to know more about when and how to collect wait statistics? Erin Stellato ([blog][1] | [twitter][2]) has [a great article on SQLServerCentral.com][3].)

If the waits are high and performance is bad, you can work to solve the problem immediately. If the performance on the server is acceptable, with a baseline, monitor it so you can tell when there is something abnormal going on, and then can work to fix the problem.

You can try to spend all of your time eliminating waits, or eliminating what you think are “bad” waits, but you may be wasting your time. Focus on what is truly important, what is really a problem.

 [1]: http://www.sqlskills.com/blogs/erin/
 [2]: https://twitter.com/erinstellato
 [3]: http://www.sqlservercentral.com/articles/baselines/96270/