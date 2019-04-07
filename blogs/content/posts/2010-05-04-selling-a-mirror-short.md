---
title: Planning your hardware for SQL Server Mirroring
author: Ted Krueger (onpnt)
type: post
date: 2010-05-04T11:26:08+00:00
ID: 780
excerpt: 'Hardware can be a single point of high performance and a single point of failure.  In a mirroring situation, there is not a listing we can put on paper as to the best hardware and configuration we can make.  Each database server is configured as it requires for IO operations, memory usage etc…  RAID 1+0 would give you write performance on the mirror but at the cost of what if you database server that is active is on RAID 5?'
url: /index.php/datamgmt/datadesign/selling-a-mirror-short/
views:
  - 7961
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - high availability
  - mirroring
  - sql server 2008

---
## A goal?

The second part to the mirroring series will discuss hardware selection. Hardware can be a single point of high performance and a single point of failure. In a mirroring situation, there is not a listing we can put on paper as to the best hardware and configuration we can make. Each database server is configured as it requires for IO operations, memory usage etc… RAID 1+0 would give you write performance on the mirror but at the cost of what if you database server that is active is on RAID 5? We could also state that configuring the mirror instances hardware for high write and logging operations would be the best case scenario. This of course would make the concept of a High Availability landscape performance better for us. Remember that the application in the HA landscape must wait for SQL Server to commit on all sides of the mirroring instances prior to returning output. 

As you can sense in the above statements, there is a resounding, “But” coming. First, let’s define a mirror and the true description of High Availability. 

High Availability has one goal. That goal is to protect us from a disaster with limited disruption to our state of the business and data services provided. No downtime! In order to achieve that primary objective with mirroring, we must plan for the event the mirror itself becomes the primary source of data services. Now, given a configuration of a mirror to allow for the absolute best performance we can imagine while in a synchronizing state, we can successfully achieve a high performance mirroring solution. However, if we failover, does this configuration fit into our primary objective of limited interruption to the data going to the business? In many cases, the answer to that question is no. 

## Let’s get to the point.

When configuring your hardware for your principal SQL Server instance, we spend countless hours making the hardware fit the aspects of the way data is written and retrieved for our unique variables. Each component of the hardware set is chosen for the reasons of making the primary objective of serving the data to the business in the highest performing way that it can. **_In the case when a mirror is introduced to this, hardware must be identical to the principal_**. Yes, there is a higher cost in mirroring than initially thought. That cost is the hardware you begged for in order to ignore the primary SQL Server(s) that runs the business. There is some catches in your proposals for added cost of the mirror hardware. Remember, licensing is still setup for SQL Server in which your mirror does not have to be licensed while completely acting as a mirror. In an enterprise landscape, that can equate to thousands of dollars saved. Thanks to Microsoft for that layout and please, never change it. 

Why does hardware need to be identical when it can possibly hurt the performance of the logging operations that goes along with a mirrored database? The answer is quite simple. Consider the event you failover to your mirror and the primary server has taken the, “worst case scenario”. Always plan for the worst case scenario! This is the event that we will make your heart jump out of your chest because you realize that the server is completely lost. In this event, the mirror has quickly become your single largest data services asset to the business and there is no date set for the primary server being replaced. 

At this point if your mirror is configured to save costs and stripped to the bones, the phone will start ringing from the horrible performance that the business endures. Even if the hardware is present on the mirror to handle the normal day-to-day transactions, if the mirror is configured outside the planned configurations that the primary once had, you will find yourself struggling to keep the performance of the mirror at the high level the primary was at. In a worst case scenario the mirror then would require hours of reconfiguring it to perform to the business needs. For instance, if we had 30GB of RAM on the principal but went cheap with 4GB on the mirror due to the mirror underutilizing RAM, the affects after the mirror becomes the principal would be extreme. During that time, reboots would be prevalent and several minutes if not hours of downtime would follow. We just lost our objective and failed to provide the solutions we needed to. So when configuring and planning the hardware for your SQL Server mirror, sell the entire HA solution to the business to acquire the funds needed to be successful even in the event the mirror becomes the principal. 

## When the day is done



<p align="center">
  In the end when your principal database server looks like this
</p>

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/hardware_1.gif" alt="" title="" width="254" height="166" />
</div>



<p align="center">
  Don’t mirror it to this
</p>

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/hardware_2.gif" alt="" title="" width="305" height="34" />
</div>