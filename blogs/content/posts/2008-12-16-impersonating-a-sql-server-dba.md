---
title: Impersonating a SQL Server DBA
author: Ted Krueger (onpnt)
type: post
date: 2008-12-16T16:16:47+00:00
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/impersonating-a-sql-server-dba/
views:
  - 4845
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server Admin

---
Not only in today&#8217;s economy but for years developers and system administrators have been pushed into playing the role as a DBA. Why? The answer is simple really. Business is about money and the more you spend the less you make. Being a DBA I know how bad it is to short staff by using one of those roles as a split DBA, but I also understand the business aspects of the decision. I was a developer for years and had the chance to become a DBA and things worked out very well in my case. I knew quickly the path my career was going to go based on the enjoyment I was getting out of working on database servers.

Knowing there are people out there in the split role position I thought it would beneficial to point out a few key things that are critical to understand and read up on. 

This would also play nicely for a Jr. DBA.

  * If you do not already understand hardware take the time to understand how configuration of a server impacts your scalability and performance. 
      * Memory
      * Disk considerations such as file placement on spindles and dual channel configurations
      * Processing power. 
      * OS editions. Standard vs. Enterprise and memory usage on 32bit. 
      * Internal disk plus external disk (Storage Area Network, Network Attached Storage etc&#8230;)
  * Focus on disk configuration. RAID, controllers and such. High writes vs. reads and what RAID is better. This is key to disk performance and if not done correctly will kill performance
  * Learn about indexing. Using SQL Server tools like Database Tuning Advisor. Microsoft does a good job giving you tools to assist you so use them. Read about clustered, non-clustered and affects and differences. 
  * Learn how to create statistics. It is not good enough to check &#8220;create statistics automatically&#8221;
  * Understand how the database engine works from transaction in, to out 
      * Understand execution plan basics
      * Table scans, index scans and seeks
      * Sargable conditions (index usage)
  * Attempt to sell the purchase of monitoring and management tools from third party companies like Quest and Idera
  * Disaster/Recovery IS YOUR JOB! Read on Mirroring, log shipping and replication. You don’t have to be an expert to setup a mirror. It is that easy
  * Clustering is better in Windows 2003 and 2008. Look into it.
  * Understand fragmentation and data storage in SQL Server
  * Understand common performance issues like Blocking and deadlocks, high cpu times, disk queue lengths and how to troubleshoot them or at least gather data on the events.
  * Database modes and affects for recovery and performance
  * Basic locking knowledge.
  * Learn the basics of SSIS 
  * Get familiar with SQL Server Agent and scheduling tasks. The more you automate the more time you have to be the other person.
  * Never and I mean never agree to a developer just because you want to get rid of them. Be critical of what you put on the database servers. If it goes down you&#8217;re the one they will come after. 
  * Security is the last item. I cannot stress enough about security. Use roles and schemas to manage things. It will make life easier for you and allow less time spent managing. And of all don&#8217;t give ANYONE the &#8220;sa&#8221; password!!!

I think that&#8217;s a good start. Remember, we don’t all need to be SQL Server MVPs to be a successful DBA. Just having the knowledge of knowing where to look is power in itself.