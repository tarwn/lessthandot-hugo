---
title: Read from your mirror in SQL Server
author: Ted Krueger (onpnt)
type: post
date: 2010-02-03T12:13:02+00:00
url: /index.php/datamgmt/datadesign/read-from-your-mirror-in-sql-server/
views:
  - 9483
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
This will primarily be an extension into the Wiki section on [SQL Server Admin Hacks][1]. 

## OLTP and Reporting

Reporting from OLTP (Online Transactional Processing) databases can be nothing short of difficult at times. The process of reporting of these types on databases can cause blocking, long wait times and in some severe cases, complete failure on the part of the high level operational requirements of the database. In the stage in which we consider how reporting will affect our databases and how to react proactively to the constant is during the architectural phases of your database server landscape. In regards to SQL Server, this means the feature set that you weigh in for each edition available to us. 

A perfect example of editions in SQL Server that provide us the means to better handle our environmental variables and reporting is a comparison of Enterprise to the less feature packed editions. With the advent of SQL Server 2005 and the major overhaul of SQL Server as a database server in enterprise installations, snapshot abilities were introduced. Of course you get what you pay for as with anything. Snapshots are only available to date in Enterprise Edition of SQL Server. You may complain, but I’m all for the marketing of this. There must be a reason to step up to this level and the feature set is that reason. It is important to understand that the SQL Server engine itself does not change across the edition levels. SQL Server in my opinion is still at each edition, a true enterprise functioning landscape with the engine and will bring great benefits to most installations. 

## Getting to the solutions

Snapshots are the key in this article because they provide us with the ability to not only create views of data easily accessed at a point in time, but they give us the ability to read from a secondary database in mirroring. 

> <span class="MT_red">Note: snapshots are just that, &#8220;snapshots&#8221;. They do not give us a real-time active ability to read data as it is committed to the database.</span>

Typically in most installations you will never be able to remove all reporting from OLTP databases. There is still business rules that require the user community to see the state of the data as it is at the time of report execution. This is also not a bad thing and with proper indexing and tuning, you can be very affective at limiting the foot print your reports and query executions leave on the database. Looking over my career, I came up with an 80/20 basis of reports on OLTP databases. This equates to, 80% point in time reporting to 20% real-time reporting. 

> <span class="MT_red">Note: when discussing reporting needs with your business, you may find yourself in the predicament that the business thinks they need real-time reporting. Dig deep into the true business needs however and you may uncover and come to an agreement that point in time reporting successfully meets the requirements. This is one of the highest means of tuning active reports on OLTP installations.</span>

There are other methods that should be noted to point in time reporting including but not limited to

  * Full Database Restores
  * Log Shipping
  * Replication (snapshot primarily)

These three that are mentioned have their advantages and disadvantages. Take all of them into your own planning stages as viable solutions. 

In the following setup we will work through setting up a snapshot on a mirror to answer the main question of how we can take advantage of a mirror for reporting. This can be a powerful aspect to your reporting infrastructure and also give you expansion to the data that in early version was not available. 

To fully show our lab and setup steps, I will be using the mirror that we setup in, [Using mirroring to Reduce Migration Downtime (Part 1)][2]. Once we have the mirror setup, creating the actual snapshot from the secondary in the mirroring configure in a T-SQL statement.

<pre>CREATE DATABASE NEEDTOMOVE_20100203072007 ON
(
NAME = NEEDTOMOVE,
FILENAME = 'C:NEEDTOMOVE_20100203072007.ss'
)
AS SNAPSHOT OF NEEDTOMOVE</pre></p> 

Once you have done this the snapshot is available as a read-only, point in time representation of the mirror. You can locate the snapshot in SSMS under the &#8220;database snapshots&#8221; tree located directly under the system databases section.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/snaps.gif" alt="" title="" width="290" height="224" />
</div>

Now any read operation can be done on the snapshot supporting any reports that meet the needs of the data that has been captured. 

> <span class="MT_red">Note: naming conventions of snapshots are typically database name joined with the point in time of the snapshot execution. This allows an easy way to maintain which snapshot holds the data you require. </span>

## It depends?

In the note I mentioned that the naming conventions typically use the date and time to show the time the snapshot was taken. In several of my installations this isn’t the case though. For reporting the data source can be very dynamic in nature but for many situations the data requirement can simply be the state the data was given a past time. In this case you can drop the snapshot and create it under the same name. This simplifies the needs in your reporting from platforms such as SQL Server Reporting Services. 

## Closing

What we went over may weigh heavily on asking for the added budget to your new or upgrading strategies for SQL Server. I hope this will help in justifying those added costs when you take the task of licensing on with your own company.

 [1]: http://wiki.ltd.local/index.php/SQL_Server_Admin_Hacks
 [2]: /index.php/DataMgmt/DBAdmin/move-databases-to-new-server-with-little-1