---
title: 'The Lazy DBA Series: SQL Trace / Profiler'
author: Ted Krueger (onpnt)
type: post
date: 2010-08-20T11:05:50+00:00
ID: 875
excerpt: 'SQL Trace (and use of profiler as a front end to SQL Trace procedures) is far from a secret.  Performance tuning in the days of 2005/2008 has become much easier with the advent of DMVs but SQL Trace still has its place in the realm of, "Why is this happening".  Performance issues real-time and tuning becomes almost, easy while watching exactly what it going on with the batches hitting out database servers.  The only downfall is the impact SQL Trace plays on an active database server.  This can be done strategically and with minimal impact from a well thought through plan of attack.'
url: /index.php/datamgmt/datadesign/lazy-dba-sql-trace/
views:
  - 8037
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - database administration
  - sql server
  - sqlpass

---
<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/lazydba.gif" alt="" title="" width="378" height="378" />
</div>

SQL Trace (and use of profiler as a front end to SQL Trace procedures) is far from a secret. Performance tuning in the days of 2005/2008 has become much easier with the advent of DMVs but SQL Trace still has its place in the realm of, “Why is this happening”. Performance tuning becomes almost easy while watching exactly what is going on with the batches hitting our database servers. The only downfall is the impact SQL Trace plays on an active database server. This can be done strategically and with minimal impact from a well thought through plan of attack. 

So what does SQL Trace have to do with being a lazy DBA?

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/lazydba_1.gif" alt="" title="" width="374" height="239" />
</div>

**Let me define what this series of blogs will show.**

_**Lazy DBA**: An efficient process of utilizing automation, native tools and or third party tools to assist in showing problems, possible solutions and active and long term monitoring of our database servers._

So we can see that being lazy in reality has little to do with being truly lazy. It is the art of being better at our jobs with speed and quick results while not writing some nasty TSQL script for five days to find out the worst running query that is stored in cache. Now that we have defined lazy in the best of ways, let’s look back at the SQL Trace functionality. SQL Trace has the ability to show us everything that an application is sending to our database server. True statement! 

**Scenario time:**

_You are the only DBA in the entire world employed at your company. This means you are the lucky and only person that does reporting, integrations, import/extract…All of it! Director needs to update the ERP system for every single one of the ten thousand customers that are in the database. You know the database but know it will also take you days just to determine just what columns need this update due to the most awful naming convention in a database you have ever seen._ 

You are in for all-nighters for at least two days because this director holds your job in their hands. What if I told you that you could get it done in around 20 minutes with these steps?

  1. Identify the columns the data is pulled from (5 minutes)
  2. Import the new data that needs to be integrated (1 minute)
  3. Write a select on the identified columns joined to the updated data (5 minutes)
  4. Backup the table(s) (2 minutes)
  5. Change SELECT to UPDATE with little alterations to the original query (7 minutes)

OK, realistically that might be 22 minutes if the tables are big. Might even be 22 hours if they are REALLY big. You get the point and yes, it depends.

The key problem here is the identification of the columns. That in reality takes up some time when you have extremely large (or small) databases that you don’t have the entity relational diagram memorized. I still don’t have AdventureWorks memorized, so that isn’t so farfetched. 

Here is how to do this as a lazy DBA. Open SQL Server Profiler. Set up a typical default template trace. Connect it to the database server (preferably development). Before starting profiler, open the application now or get someone that can open it to do so. Get ready to open the screen that shows the data as it passes through the application. Hit start on the trace and then go into the application view to edit the data. Notice I said edit. Some application that use views or other accessing methods may make this more difficult than it needs to be. Typically, edit is a safe bet that you get down to the base tables.

Stop profiler immediately once you see the transaction go by or know the window in the application is fully loaded. 

Depending on the method you used to store the profiler output, open either the table or trace files. Use a LIKE statement to find the entries that consist of your login used first. Then refine to the actual data you used in the application to filter on. Such as customer or item number that you used to get to the screens. 

Refine that down (on minute 3 or 4 now) and then grab the columns. (Copy/paste takes at least a minute)

Next step, import the data that needs to be used to update the database. Hopefully they made this easy on you and didn’t give you TPS report printouts. Write your SELECT statement and verify that the return count matches the update count (or more depending on requirements) and then validate they match up on the key relationships that you used to join on. 

Always make sure you can recover, so make a backup of the tables in question. Keep in mind constraints! Don’t EVER remove them just because it might work. Data Integrity is important to you!

Alter your statement to an UPDATE on a join or derived table and execute it.

This is the best part: Email the director and say it is all ready for them to validate in development. Sit back and relax now. You were just lazy and it paid off by wowing them.