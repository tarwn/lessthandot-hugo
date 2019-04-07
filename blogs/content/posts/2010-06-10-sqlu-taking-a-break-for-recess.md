---
title: 'SQL University –  Recess time!'
author: Ted Krueger (onpnt)
type: post
date: 2010-06-10T17:10:13+00:00
ID: 817
excerpt: 'The recess bell just rang for SQL University HA / DR classrooms.  While all of the SQL kiddies are running around the playground and playing with the things they have learned over this semester, the chalkboard is going to get a workout so when they get back, they can take the notes they slacked on earlier.'
url: /index.php/datamgmt/dbprogramming/sqlu-taking-a-break-for-recess/
views:
  - 6444
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - backup
  - disaster recovery
  - dr
  - ha
  - high availability
  - log shipping
  - mirroring
  - restore
  - sql server 2008
  - sql university

---
The recess bell just rang for SQL University HA / DR classrooms. While all of the SQL kiddies are running around the playground and playing with the things they have learned over this semester, the chalkboard is going to get a workout.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/bart.gif" alt="" title="" width="628" height="339" />
</div>

<p align="center">
  <i>Image courtesy of <a href="http://www.addletters.com/pictures/bart-simpson-generator/233605.htm">Bart Simpson Chalkboard Generator</a> and <a href="http://toadworld.com/BLOGS/tabid/67/EntryID/543/Default.aspx">linkback to Jeff Smith excellent article</a></i>
</p>

Over the last week we’ve gone over a lot regarding HA and DR. The first day we defined situations and the key factors that are needed to be successful in obtaining secure data services and high availability of those data services. Any of these two strategies to protect our data against disasters, local and remote; always start with the definitions required to plan out the implementation. We learned together that just throwing things like log shipping into the mix may not truly give us the protection we need. This would happen if we leave important business entities out of our planning and document how our systems would come back from disasters. 

We done this over the week by showing the features that SQL Server has to offer without much added cost. When budget is available, we also discussed briefly landscapes like Geo-clustering, SAN replication and truly next to real-time mirrored data centers for recovering in the event of disasters. These were terms in passing so we didn’t venture off the scope of each class but their importance is there nonetheless. 

We defined a list of the important notes we set off to discuss that effect the decisions of how we can accomplish HA and DR

  1. Size of databases
  2. Network capabilities
  3. Budget
  4. Features available per edition of SQL Server
  5. Maintenance load
  6. Allowable downtime
  7. Personnel resources required
  8. Initial setup downtime
  9. Will DR fit into the HA strategy?
 10. Documentation – knowledge transfer
 11. Can we test this?

Automation of Disaster and Recovery played an important part in our discussions. Do we automate is the key to decide in your unique strategies. DR typical is a manual failover process while HA only becomes HA when automation is playing into the events of a localized disaster.

SQL Server backups were stressed on day two as the foundation of all that is DR (and HA recovery). With careful planning and testing on both HA and DR, we achieve secure and always available data services. But when even that strategy fails, we must turn to our backups to recover both the data and the DR and HA strategies themselves. 

Today we discussed log shipping as a cost effective disaster and recovery method. Log shipping is a great alternative when budgets are low. Problems do arise in the effectiveness when our data services are larger than our check books though. All-in-all, log shipping can provide security in a DR situation and leaving it as a possibility when strategizing is always good. 

So now we move to our last day and we will discuss High Availability. The class will show database mirroring in our newer versions of SQL Server. We’ll discuss things like split-brain scenarios in mirroring and certificate usage so we can mirror across different domains. Finally, we need to briefly discuss SAN replication and geo-clustering again. Although we will be brief on this topic, we want to emphasize them because when the budget is there, nothing really matches them for real-world HA.

**Recess is now over so let’s get back to work!**