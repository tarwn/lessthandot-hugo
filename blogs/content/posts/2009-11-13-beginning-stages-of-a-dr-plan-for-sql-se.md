---
title: Beginning stages of a DR plan for SQL Server
author: Ted Krueger (onpnt)
type: post
date: 2009-11-13T14:06:44+00:00
ID: 622
url: /index.php/datamgmt/datadesign/beginning-stages-of-a-dr-plan-for-sql-se/
views:
  - 9676
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - IBM DB2 Admin
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
I’ve found that many companies find out what true Disaster Recovery is only in the presence of a true disaster. Obviously this is not a very optimal time to start thinking about what could have done to keep the money flowing through the veins of the company. 

In the near future I will be writing a series based around DR. The series will be mostly geared towards your SQL Server instances and some tricks and tips to make recovery quicker for you. I&#8217;ll also be touching on some important factors that are not commonly part of planning. Some of those are

  * DBA object recovery. Includes the objects you use to do your job
  * The decision to failover
  * Business buy-in
  * Business Impacts
  * Business failover 
  * Having the business ready for a disaster

However before we get into these details we need to cover a more important topic, the DBA.

## The DBA &#8211; The Most Critical DR Ingredient

DBAs are the most critical part of a disaster recovery plan. As DBA&#8217;s, we are entrusted to make sure the data is secure on not only the primary sites but also the recovery sites. There are many ways in which we can be successful in that goal and many are already native to SQL Server. Log shipping, replication, snapshots, backup and restore and even scripting abilities are all part of native capabilities you can use to secure an offsite solution. Some of the these features are limited to Enterprise edition but, even with Standard edition, we have enough options to cover our bases. The DBAs knowledge and analytical skills are the foundation that the DR plan is built upon, so we will look first at some of the built-in features and how they can help build out that foundation.

## What type of disaster is it?

When I start putting together a disaster and recovery plan for SQL Server, I look at disasters in different levels of recovery. For each of these levels, I put together my methods and strategies for recovering from them. Looking from a recovery point can uncover problems in strategies that may not be seen until an actual failover.

## The levels of recovery

  * Audit Recovery
  * Low level object recovery
  * High level object recovery
  * Point of failure recovery
  * Complete recovery



<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/line.gif" alt="" title="" width="100%" />
</div>



<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/easylanding.gif" alt="" title="" width="142" height="149" />
</div>

<h2 style="text-align:center">
  <i><b>Audit recovery</b></i>
</h2>

These recovery points are any requests that require analysis on corruption, financials or even data changes captured over time or are non-critical in nature. This is a slow paced recovery. Corruption would be the fine line between Audit and Object recovery. If the corruption involves any type of situation where a company process is in the stop mode, you have to make the managing decision to move the recovery level to the next phase. If these disasters are not stopping the business flow, then backups can come into play to recover. Typically, in an Audit recovery level disaster, you have the time and resources for restoring data from backups because they do not affect the flow of money like most disasters do. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/ducttape.gif" alt="" title="" width="136" height="136" />
</div>

<h2 style="text-align:center">
  <i><b>Low level object recovery</b></i>
</h2>

Low level object recovery consists of any object in the database that the company needs in order to successfully complete a transaction. A transaction is defined in many ways. Typically with any business, it means from the customer initial contact to sale of services or widgets. Objects can be non-critical tables, views, procedures, reports, database level security and other programmable objects. Good methods of recovery in this category are snapshots, test restores, UAT systems, third party log reading tools for rolling back transactions and object level recovery. In some cases, when the time is available, a restore from a backup stored locally can be achieved. 

<span class="MT_red">Tip: Typically more important tables are backed up through file groups on files in the database which are recovered quicker than a full restore in that sense.</span>
  
Low level object recovery can be a rapid recovery point most of the time. This level usually starts and ends with the DBA. Simple methods such as table backups and object versioning or scripting can prevent loss and promote quick recovery points before the disaster ever becomes critical. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/kissit.gif" alt="" title="" width="136" height="136" />
</div>

<h2 style="text-align:center">
  <i><b>High level object recovery</b></i>
</h2>

Critical objects, mirrors, replication settings, instance level security and service critical objects like endpoints and message queues are a few in this level. Third party object level recovery is again in the list of tools to recover these types of objects but you will find yourself going to configuration documentation and scripts much more here. Yes, scripts saved as a backup can be a life saver in the event of a disaster.
  
High level can fall into disabling disasters or exposure disaster. Disabling disasters explain themselves. The business flow is completely stopped at this point. Exposure disasters are of the same importance but less of an impact to the actual flow of data. Mirroring is a major object that falls into the exposure listing. If you lose a mirror, business still runs but at a cost of being exposed to a much more critical disaster. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/cablesinhand.gif" alt="" title="" width="165" height="111" />
</div>

<h2 style="text-align:center">
  <i><b>Point of failure recovery</b></i>
</h2>

This is a very high-level object recovery plan. This includes entire databases, instances, replication subscriptions and even entire facilities &#8211; but does not specifically stop the flow of money through the company. This recovery may simply mean you fail portions of your systems to your offsite DR location. This is a Disaster Recovery level but also can be blurred into HA (High Availability). The defining line between the two is that, in the case of a high recovery event, HA is typically part of the disaster and immediate loss. Essentially, this becomes a recovery point in your DR plans when HA strategies fail or are not in place at all. The largest object defined in this level is an entire manufacturing location or hub in the company’s infrastructure. One prime example is a case I went through last year when a hurricane hit our Texas facility. DR strategy&#8217;s part in the high level recovery went into effect to recover from the complete loss of communication with the facility. In my experience, this can be the most time-consuming recovery point if you don’t think about it before it happens. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/rip.gif" alt="" title="" width="134" height="132" />
</div>

<h2 style="text-align:center">
  <i><b>Complete recovery</b></i>
</h2>

Last and most terrifying is the heart pounding moments we as DBAs live for. The database has become an ex-database, it is no more. I test these types of failures by pulling power plugs on main switches. I still believe you are not fully experienced until you have been involved in one or two of these. At the same time, I wish them upon no one with a weak heart. Trust me, it doesn’t go as smooth as you think it will or your DR tests may have gone. Fire, lightning frying the entire state of Illinois, your servers under water or your mother-in-law in town; there are a lot of forces that can cause a complete failure. At this time, you and your group make the hard decision to fail over completely to the offsite location. That really is a big decision and should never be taken lightly. Many variables come into play when making the call to do this. When it does happen, you’ll probably have documented and tested how each should be brought up at your DR site. There isn’t much to say about this stage other than you need a complete replica of the systems that are needed to run the business. Remember, DR sites don’t only consist of databases. This also means you need to keep patch levels of applications, security and everything down to SQL Agent job changes up to date on the DR site. It is good practice in planning DR to assume you could lose everything and never get it back. So even knowing things like reporting services may not be critical, it’s always a good idea to have a way to get all that back by using DR methods to create replicas.

## _**Wrap up…**_

It is important to remember that all of these levels affect business and that means the business must be involved in most of them. Setting up log shipping to a server offsite is not nearly enough for a successful DR plan. The business needs to know how to start using that source and also has to buy into the effectiveness of its ability to run the business processes that make money.