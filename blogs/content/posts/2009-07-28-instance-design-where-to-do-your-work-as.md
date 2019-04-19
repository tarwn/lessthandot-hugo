---
title: Instance design; Where to do your work as a DBA and DB Developer
author: Ted Krueger (onpnt)
type: post
date: 2009-07-28T11:19:29+00:00
ID: 524
url: /index.php/datamgmt/datadesign/instance-design-where-to-do-your-work-as/
views:
  - 7437
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
One thing I stress to all the developers and DBAs I talk to is the concept of creating custom objects and where to place those objects on any particular instance. To get the obvious out of the way, we all must keep in mind that all third parties (most) come with restrictions on modifying them in any way. This meaning alteration to any objects, additional objects or process altering objects to any database on an instance. This list includes some of the following objects you may take for granted when working on a third party or even in-house designed database

Triggers
  
Stored Procedures
  
User-Defined Objects
  
Tables
  
Table constraints
  
Default values
  
and on with any database object...

Basically anything you think you can do to that database, you shouldn't. The basis of the database and the design around it was to hold the data critical to the business. When it was created, great thought went into the process flow of that data and storage ability of it. In that exercise and designing strategy, most often the creation and or alteration of the existing objects was not in the plan. Even the slightest change to some designs can break down the integrity of the landscape. not only can it break it down quickly but in some cases the problems are hidden until they are next to impossible to fix later in the databases life. This goes back to why we all were forced to learn entity relational diagramming and data definition diagrams etc... These import documents are created for strict designing guidelines. To venture out of them can design nothing short of disaster.

So what do you do? Everyone needs these objects in order to meet deliverables on reporting, external business process requirements, integration and other critical business tasks. Here is what I do and something after settling into an environment I start inserting into the landscape of each instance.

First, there is always a database on every instance that controls the maintenance tasks and heavily secured tasks that only a DBA should be controlling and executing. That database in my own environment is called, “DBA”. The name is self explanatory and if someone sees it on my instances, they immediately know at least who owns it. This database holds fragmentation logs, procedures for maintenance such as defragmenting, backup/restores tasks, custom log shipping, custom replication procedures, integration tasks to other instances, linked server definitions, functions and in some cases even views. If you just started working deep in database development or possibly a Jr. DBA, the first question you ask at this point is how to access the data around that instance from this database. Well, you should already be using fully qualified names and that is the way you do it in this case. It may seem like a basic thing to most of the seasoned experts around here but I've come into contact dozens of times where people do not know they can say, DBA.admin.DefragMyIndexes. 

The next thing that needs to be considered is how critical is this new bridge database to the business? Personally, if this database was lost on any given instance of mine, it would take me some time to recover from it. Each of these DBA databases are completely customized to the needs of that particular instance. This means there is little commonality across them. Just saying that screams full recovery mode all over it. Not only are these databases backed up heavily, but they are mirrored for high performance and also log shipped for disaster recovery. Let's face it, My Documents folders full of scripts and solutions that you would need to sift through is just not going to benefit the business if you lose this database. There is another side to this recovery model though and I want to touch on it so you are aware and prepare for it. The purpose of this database is to be the work horse that you need while not interrupting the others. That means you'll have bulk loads, possibly heavy CLR usage and long running queries. You must prepare for that concept because even knowing this database is segregated from the others; it will still affect overall performance of that instance. 

Last I wanted to talk about another concept that is almost identical but slightly altered from the DBA database. That is the creation of integrated databases or what I call, development holding tanks. If you've read some of my other blogs you will probably know that security is a major concern of mine. I take it seriously and to heart. I work hard to make sure the instances under my control are performing as well as they can and that the data is served to the proper place when it is called upon. This securly and with prevention of a slipped F5 in the wrong place from happening. That being said, we have to consider database developers in the scheme of things. Primarily report writers are the victims of my strict security guidelines. To me this doesn't make them victims but saves them from being the victim in the long run. Most of them don't see it that way and I'm just an @$$ but I know in the long run it benefits both of us. Under no circumstance should a developer have access to CREATE or ALTER on any database other than a specifically designed one for the tasks they need to complete. You know as well as I do that if a developer has these rights, they will use them. So this brings in other databases I have come to design that are direct links into the databases with strict security restrictions. A good example of this is ERP systems. On the instance(s) that holds the primary ERP systems data store, there is a coexisting database called ERPLINK. The developers and other systems people have much more freedom here. That freedom is still controlled and everything is reviewed with careful consideration to the effects on the other databases, but it allows the developers to work without completely being tied down. Match this landscape in your testing environment and it will make transports even easier as well. Remember to create your schemas structured well i both of these types of databases. admin, developer are a few I use. You can also consolidate by creating schemas named appropriatly to the links they are used for. For isntance if you have ERP as a database and in ERPLINK you have DevERP. 

So there it is. I feel strongly if you follow these guidelines you will make you instances much easier to maintain all around for yourself as a DBA and for the development going on around it. It will save you from later hardships of realizing you probably shouldn't have done certain things to certain databases and maybe that person really didn't need all those rights.