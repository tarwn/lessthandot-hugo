---
title: "The Lazy DBA Series: Email me,  I'm broke!"
author: Ted Krueger (onpnt)
type: post
date: 2010-08-24T10:40:42+00:00
ID: 878
url: /index.php/datamgmt/datadesign/lazy-dba-email-this-stuff/
views:
  - 8764
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server

---
I knew a DBA once that had "The Routine" and they stuck to it every single morning. (I laugh a bit every time I say, "The Routine" while thinking of this DBA). The DBA actually named the steps that were to be done every morning as, The Routine. Each step from the laptop plugging into the docking station, to opening QA before opening EM was in a specific order and never deviated from. (For the younglings, QA is Query Analyzer and EM is Enterprise Manager)

I recall several of these steps; checking SQL Server Agent jobs one by one, reading the error logs on each instance and even checking the nice little green indicator that indicates SQL Server was running. So what's wrong with that? Not one thing! Well, except we had around 100 SQL Server instances at that time, hundreds of Agent jobs and some poor fool never knew about sp\_cycle\_errorlog so the logs were huge. You can imagine that this entire process took hours and caused problems with project scheduling given the, "Well, I can give projects an hour a day". 

Ever since that lesson I had from observing another DBA for the first time, I quickly took the power of email and automation to heart. So much to heart that I still, to this day, insist that everything can be automated (to a certain extent). I can't see myself reading thousands or even hundreds of log entries or sifting through the same logs in the SQL Agent history log viewer. You know, that thing is slow!

I won't try to fool anyone reading this, SQL Server 2000 and previous versions were downright horrid for email abilities. Install Outlook to setup sql mail??? WHAT? Can I say, Bite me in a blog? Oh and let's not forget the external sp_ calls to CDOTNS.DLL. They did work though because I refused to install Outlook on a database server to date. So even back then I was setting up email notifications based on events in SQL Server. They were nothing near what we have at our disposal now, but they did work.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/lazydba.gif" alt="" title="" width="378" height="378" />
</div>

Jump ahead 300 years in computing years and we have SQL Server 2008 R2. I'm impressed! Did I mention that? SQL Server has evolved and done so very rapidly while showing growth that leaves itself with the top database servers out there.

SQL Server 2008 (R2) provides us with Extended Events, Default Trace, DMV/DMF and Database Mail just to name a few ways we can use automation for events and review of daily and real-time maintaining of data availability. I like the Lazy DBA feel to all these new awesome features. So where is an example of this goodness we speak of?

**Extended Events**

Let me first say, wow! Extended Events are event driven logging that has the ability to capture information that is vital to troubleshooting and automating being truly proactive in finding and resolving problems before they begin. And that was a mouthful but Extended Events have given us a mouth full in the ability to really key in on problems and events as they happen.

There are several things we can do with Extended Events. Events like Deadlocks and System Resources usage to name two major events. Jonathan Kehayias ([Twitter][1] | [Blog][2]) covers Deadlocks in great depth [here][3]. Jonathan also wrote one of the best whitepapers I've seen on Extended Events to date [here][4]. A good example of system resources and Events is CPU usage. Everyone has had CPU problems at one time or another. Even [BOL][5] notes the use of Extended Events for troubleshooting CPU. Really, it is that useful of a method to monitor and troubleshoot CPU problems.

I think we can mostly agree that bad execution plans cause high and out of control CPU usage. It is commonly the first place to look when your CPU suddenly jumps and hovers around the 50-100% mark (in my case, getting the tremors when it hits 20% for longer than a second). I'm not going to rewrite the same code that is out there to set up Extended Events to capture high CPU because Paul Randal ([Twitter][6] | [Blog][6]) does a very good job of that [here][7]. And this series of Lazy DBA articles are meant to be quick and short.

The following statement from Paul's blog shows the test for events and where email notifications come into play 

```SQL
SELECT COUNT (*) FROM sys.fn_xe_file_target_read_file
   ('C:SQLskillsEE_ExpensiveQueries*.xel', 'C:SQLskillsEE_ExpensiveQueries*.xem', NULL, NULL);
GO 
```
</p> 

Can you test this in an Agent job with T-SQL or SSIS package? Yes! And then using sp\_send\_dbmail, you can attach the result from the fn\_xe\_file\_target\_read_file into an email and send it off to the lucky DBA winner of the day. You just found a problem by not even looking at a database server!!! 

Wait a minute! Is that real-time? It depends on your definition of real-time. Mine doesn't define that as true real-time notifications of events. It does however tell me quickly enough when a problem is there. Wait for it...[Event Notifications][8]. The name says it all. Event Notifications are notifications designed to notify you upon the action you have defined. This can be DDL events and SQL Trace events. SQL Trace events opens a big range of things we can actively notify when they come up. Deadlock events are common notifications. Before we had to go to lengths like, [Proactive Deadlock Notifications][9]. Now this is extremely [simplified with Event Notifications][10]. 

From what we've touched on, we can come to a conclusion that for one, being Lazy is cool. Second, what we have now in SQL Server is a step in the right direction and had given us only one problem. Our excuses for not knowing when events and problems occur is all but NULL. 

**Congratulations, you are off to being Lazy and more efficient.**

 [1]: http://sqlblog.com/blogs/jonathan_kehayias/
 [2]: http://twitter.com/sqlsarg
 [3]: http://www.sqlservercentral.com/articles/deadlock/65658/
 [4]: http://msdn.microsoft.com/en-us/library/dd822788.aspx
 [5]: http://msdn.microsoft.com/en-us/library/bb630354.aspx
 [6]: http://twitter.com/paulrandal
 [7]: http://www.sqlskills.com/BLOGS/PAUL/post/Tracking-expensive-queries-with-extended-events-in-SQL-2008.aspx
 [8]: http://technet.microsoft.com/en-us/library/ms175854.aspx
 [9]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/proactive-deadlock-notifications
 [10]: http://weblogs.sqlteam.com/mladenp/archive/2008/07/18/Immediate-deadlock-notifications-without-changing-existing-code.aspx