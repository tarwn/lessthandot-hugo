---
title: SQL Server Agent Job history purging
author: Ted Krueger (onpnt)
type: post
date: 2009-04-10T09:55:29+00:00
ID: 380
url: /index.php/datamgmt/datadesign/sql-agent-job-history-purging/
views:
  - 12786
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
Most often early in a DBA's career they will go off on a job creation spree. This is actually good. I've always gone with the feeling that nothing cannot be automated. The agent gives us that freedom of automation and notification on tasks. Schedules can be robust, most tasks can be run and you can strategically manage tasks to prevent them from tripping over each other. The problem that you may run into is a poorly configured SQL Server Agent. More or less you simply set the agent to start up upon data services start up and you forget about it. CPU idle times, time out settings and history management are ignored.

Well at some point you are going to find yourself trying to few a jobs history and from within SSMS it will either timeout or take hours to load the history. This can be a bit annoying when all you want to see is the last failed job. History is important so you should have quite a bit of it. This is how you'll understand run time trends and base values to determine how to schedule additional jobs. If you allow history to build however you'll soon find yourself with around 2 million rows of headaches. When you realize this finally SSMS has proven to be not so friendly as most GUI interfaces can be when it comes to doing a large amount of work on data. You will try to go into SQL Server Agent properties, finally see that "Automatically remove agent history" and tick the check box. Problem is when you hit OK your session deletes everything older than your set time (default 4 weeks). If it went to the point there is a mass of data SSMS gets a bit clunky.

So to make it easy we have the sp\_purge\_jobhistory procedure. To run you have 3 parameters. job\_name,job\_id and @oldest_date. As with any job related system procedure you can only specify name or id but not both. Nice thing about the system procedure is you can leave jobs reference off and remove all jobs history. This includes enabled and disabled jobs.

So to make this simple and much quicker so your computer doesn't lock on you execute something such as

```sql
Declare @oldest datetime
Set @oldest = Getdate() - 14

Exec sp_purge_jobhistory @oldest_date = @oldest
```
And then please, don't forget to configure a valid retention time in the agents properties 🙂