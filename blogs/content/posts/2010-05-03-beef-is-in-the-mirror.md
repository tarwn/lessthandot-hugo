---
title: Planning your SQL Server mirroring landscape
author: Ted Krueger (onpnt)
type: post
date: 2010-05-03T12:29:15+00:00
ID: 777
excerpt: Researching and obtaining the knowledge and test cases for configuring mirroring 
  is a large part in putting SQL Server mirroring to work for your High Availability (HA) 
  solution.  Before you start jumping into configuring mirroring, several questions should 
  be researched and answered.  This ensures that the HA solution and mirroring will work 
  for your environment and your business.  One of the highest achievements in mirroring is 
  to ensure it will always be invisible to your user community.  After all, as DBAs, our 
  position is one that truly is successful when the phones are not ringing and users forget
  who we are.  Our positions have little to no recognition from their view.  It's a hard 
  truth that we most of the time are far outside the spotlight, but one that is key to the 
  role.
url: /index.php/datamgmt/datadesign/beef-is-in-the-mirror/
views:
  - 7358
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - high availability
  - mirroring
  - sql server 2008

---
Researching and obtaining the knowledge and test cases for configuring mirroring is a large part in putting SQL Server mirroring to work for your High Availability (HA) solution. Before you start jumping into configuring mirroring, several questions should be researched and answered. This ensures that the HA solution and mirroring will work for your environment and your business. One of the highest achievements in mirroring is to ensure it will always be invisible to your user community. After all, as DBAs, our position is one that truly is successful when the phones are not ringing and users forget who we are. Our positions have little to no recognition from their view. It's a hard truth that we most of the time are far outside the spotlight, but one that is key to the role. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/mirror_plan_1.gif" alt="" title="" width="404" height="304" />
</div>

## The business

Making HA invisible not only makes recovering seamless, responsive and allows for the business to achieve its own goals of making money but keeps the spotlight pointing in the right direction.
  

  
A few key points to think about in the planning stages of mirroring start with the business.

  * Does my business need to be “mirror aware”?
  * If so, what actions and support do we need to successfully failover to the mirror?

This is the utmost crucial point in why we put HA in the background. The business needs to run. That's the fundamental basis for why HA is ever considered as an objective. Don't forget to have them involved in this planning. If they are not and the business entities are required to be part in a failover, the situation simply adds a scenario that causes delay.

## The backbone

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/mirror_plan_2.gif" alt="" title="" width="369" height="278" />
</div>

  * Have in place the network backbone to handle the mirroring and full recovery

Now let's take a look at and even more critical part to the mirroring planning stages; the network. Take a scenario of an index maintenance task. When an index rebuilds, it causes a large amount of information to be written to the transaction logs. This information in a mirroring landscape, no matter critical or not, is then sent to the mirror. Is the rebuild maintenance handles several gigabytes of data, all of this will be sent across the network at the time of the maintenance. You can already see, even in an off hour maintenance time, the amount of data that is being sent could very well could flood the network and send the mirror into a state of synchronizing. This leaves vulnerability in your recovery plans. 

## Time to learn

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/mirror_plan_3.gif" alt="" title="" width="313" height="210" />
</div>

  * SQL Server mirroring research and knowledge

The decision process must happen now from the SQL Server skills side. Mirroring is not a hard HA solution to setup and manage. Not many will argue that from a SQL Server perspective. However, mirroring on the surface may seem simplistic, but the under armor of the HA solution has many points of failure if not thought out ahead of time. 

Let's take a look at a few of these “under armor” options. First we look into the true safety on, full automated failover mirroring landscape. In order to be success in implementing a automatic failover mirroring solution in SQL Server, you need 3 key pieces in place

Principal, Mirror and Witness

This solution will also require a synchronous mirroring mode. In short, this requires committing of transactions on the mirror (and principal) prior to the application being sent the “ok” to move on. If you are in a situation where that data being committed is large enough, the network comes back into contention as a major performance aspect. The application itself will also be required to handle this delay. Again, if the data and processing is extremely large in size or complex, timeouts can be more relevant than previously considered. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/mirror_plan_4.gif" alt="" title="" width="254" height="205" align="left" />
</div>

Next we can move to a performance added mirroring solution that runs in asynchronous mode. High performance mirroring is only an option in Enterprise Editions. There are no future changes for adding Standard to this feature. Now cost is a major factor. We all want Enterprise, but do we really need it? With asynchronous (High Performance) mirroring, there is also no built in automatic failover options. Although there are methods to get around this with monitoring the state of mirroring and acting on your own scheduled jobs to handle “automatic failover”, at this point it truly isn't automatic. In some cases asynchronous mirroring with your own failover scripts can be beneficial. Finding your mirror in a state that you require a failover of your applications allows you to start reconfiguring them as needed. Having the Failover switch in connection strings simply is not an option often. This requires either registry changes, custom provider reconfiguring and in some cases, security modifications to handle the mirror successfully being utilized.
  
Synchronous mirroring can take on a different approach to safety as well. In short, we can have safety without automated failover. This type of landscape is good for environments that have the infrastructure and application support to handle the commit overhead of synchronous mirroring while not requiring or wanting automation of a witness. Instead of reacting to a failover, we can react and failover. This promotes a cleaner and more stable failover in theory. 

All methods of mirroring have their place in your particular setup. SQL Server allows us just enough options to allow us to manage a HA backbone to our data services. 

The problems then would be? 

There are some issues with mirroring in general that can be addressed with utilizing the resources in the mirror itself. With Enterprise, we have snapshot capabilities. This is extended by having the ability to run snapshots on our mirror databases. With this we open reporting solutions off data sources that limit the problems that go along with reporting on OLTP based systems. There are drawbacks to snapshots in asynchronous mirroring however. Often, asynchronous mirroring leaves the mirror in “synchronizing” state. This means the log is in a mode that we cannot issue a snapshot off of. Keep this in mind when your database is large enough to the point a snapshot may take some time to create. You will be required to snap the database at a period of low transaction times in order to prevent problems in the mirror itself. 

## Hardware, hardware and more hardware

Hardware is the last in planning stages. What type of system do I need to be successful in a mirroring landscape? This question is asked often. The recommendation for this is the mirror hardware as much as you mirror the databases. If you have planned out and sized the hardware on the principal already, don't skimp on the mirror. In the case of a disaster in the event HA takes over and the mirror becomes the principal, the last thing wanted is a underpowered mirror that has only been sized enough to handle the mirroring itself. 

## By the end of the day

After all the planning and configurations have been put in place, SQL Server mirroring solutions will be sound and stable. This allows for better sleeping and peace of mind that the systems can react on their own to what the nature of computing can throw at it. 

This will start a series of blogs on mirroring from a request I received. They will consists of the following topics so look for them to come

  * Planning your SQL Server mirroring landscape
  * Planning your hardware for SQL Server Mirroring
  * Mirroring landscape hands on with Developer Edition
  * Mirroring the default instance and other endpoint catches
  * Mirroring modes (to sync or async)
  * Customizing your failover routines in Mirroring
  * Abuse your mirror for other tasks</p>