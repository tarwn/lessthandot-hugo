---
title: 12 ways to break your database server before the first transaction
author: Ted Krueger (onpnt)
type: post
date: 2010-05-19T10:15:58+00:00
ID: 794
excerpt: 'Defaults hurt.  They take a 3 inch splinter and try to bury it as far up and under your nail as far as they possibly can.  Every database server has a task.  A mission if you will.  That mission is to serve the data and secure the data.  Some need 32GB of RAM; some need 3GB of RAM.  This one might need 32 spindles on RAID 3 billion.'
url: /index.php/datamgmt/datadesign/configuring-sql-server-dba/
views:
  - 11303
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - dmf
  - dmv
  - index
  - maintenance
  - performance
  - sql server 2005
  - sql server 2008

---
### 1. Not adjusting memory settings on your database server

Below is a screen shot of the dreaded default 214748367 maximum server memory allowed to SQL Server. Will it take it? Yes, it will. It holds no sympathy for others and will leave no crumbs behind for mere OS operations or paging. Set this to an allowable maximum based on your server’s actual available memory. Leave some for the OS to survive. The server will thank you later. Trust me!

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/memmaximum_BAD.gif" alt="" title="" width="448" height="401" />
</div>



### 2. Installing EVERYTHING by default

Defaults hurt. They take a 3 inch splinter and try to bury it as far up and under your nail as far as they possibly can. Every database server has a task. A mission if you will. That mission is to serve the data and secure the data. Some need 32GB of RAM; some need 3GB of RAM. This one might need 32 spindles on RAID 3 billion. Point is: There are no defaults that match your installation. Do not take them for granted and do not expect them to be your saving grace preventing some research. Know what hurts even more? Installing all of the features on one server when you only need one of them can cause as much of a problem as leaving default settings in place. Try to install the features that you know will be utilized so you can utilize the resources of the server to what it is meant for? 



### 3. Enabling CLR and abusing it

CLR is a foundation, a pillar of strength and power, a stance in front of an angry mob preventing chaos, the punk rocker that stands above the exhausted and pile-driven leftovers from a slam dance session. Embrace CLR like it is hiding under the couch and can save the puzzle you spent days working on but has that one piece missing, causing nothing but pain and agony with failure to your hard work. OK, now STOP! With great power brings great failure. CLR will and can take you to the next level of strength and functionality to your trials with SQL Server as a DBA and Developer. CLR can also and will take everything it wants when not managed from your database server. This will leave it starving for more and hung in the shadows begging for a restart. 



### 4. Handing out the SA password like it was free ice cream day. 

SA is a landmark and an object not to be tangled with. Think of Dirty Harry and that monster 44 Magnum facing you down. OK, now hand the 44 over to someone and ask them to point it at you. Feel good? NO! Keep SA as your protection. Do not use it unless it is called upon in a time of need. Yes, even the sysadmin(s) should have their own security model and SA is not part of it. 



### 5. Never maintaining indexes and statistics

You’ve spent days researching and configuring IO, memory, SQL Server configurations down to the last MAXDOP setting. It truly is a remarkable feeling you can only explain by feeling it yourself. Problem is, when it stops there and maintaining the database server is left to the planning and implementation. Indexes and Statistics alike need special care. They can enforce performance beyond your most exotic dreams when used and maintained. They can become your nightmarish horrors if not maintained at the same time. 



### 6. Installing EVERYTHING including binary, data and log files to the C drive

If you are installing SQL Server on your desktop or laptop at home and have one 25GB drive, then sure, install to the C drive. Most of us have those “server” things. They have drive bays, and sometimes we even get a D or an E drive. When you install SQL Server it will want and request you to install everything to the C drive (or the volume letter you pick for the OS). It’s ok for the setup team to do this. I respect that not all best practices can be programmed into everything. So here is the almighty: If you have a data drive, log drive and OS drive or anything other than the drive for the OS, please use it for your data. Your database server will thank you when it is not contending with other IO operations. 



### 7. Relying on RAID as a recovery plan

RAID is not going to save you. Before last year or so when it was publicized that a company literally was found whimpering in the corner after losing all of their data from relying on RAID levels to protect against failures, we didn’t think it was possible to fathom this concept. Who would think this way? Well, someone did, so we need to say it. 



### 8. Think you are protected from disasters without HA or DR?

Disasters are inevitable. We cannot hide from them and they jump up behind us when we least expect it. Plan for it, test for it and become familiar with it. SQL Server has many native features that can be used to handle HA and DR. When lightning hits the building, a river decides to create a lake near your cube, a tornado says hello to the data center (face to face) or even an earthquake decides to open a nice pretty hole in the earth and eat the very data you vowed to protect, HA and DR will be there to rescue you (and your job). 



### 9. Under-sizing your installation

Sizing is an art for database servers. It is also an art form to not size your database server correctly. Not doing so will cost much more in the long run. There will be cost both in dollars and downtime (which just means more dollars – lost). 



### 10. Forgetting the network is just as important as IO performance

It’s the network! Blaming the network on slow performance is fun. Network and database administrators like to blame each other on performance being slow. The nice part about the network is if there isn’t much monitoring in place, we can get away with it as DBAs so much easier. OK, is it really the network or not? If you do not have a network that is stable and can handle your data flying around the business, your hardcore, supersonic, beefy, brat- eating server doesn’t help you at all. Get with your network group and discuss the amount of expected data. ASYNC\_NETWORK\_IO waits are not fun when you are plagued with them on a database server. 



### 11. Allowing Jane and John to write T-SQL because they know how to write SELECT *

So the CFO comes to your office and he just went to this really cool lightshow expo in Vegas to release SQL Server 30099 R500. Best lightshow he has ever seen. Even better was that the really good sales guy up on the stage popped open SQL Server Management Studio version fifty billion and started bringing up data. Yes, you guessed it. He just asked for you to install that really neat tool on his laptop so he can, &#8220;Get data&#8221;. Scary! Put policies in place to prevent this. Even for senior staff. Someone in an office with their finger on the F5 key and a SELECT * FROM reallybloodyhugeandwidetable is your nightmare come true. 



### 12. Backups are for sissies so I don’t do them or restore them to test

Everyone knows the signature. It’s comical in passing and at user group meetings. Is it true? Not even close. Backups are your foundation for recovery. Your ability to save the universe from data gremlins that chew their way through your NIC port into the chassis, feasting on the RAM before moving to your beloved disk. Wait, listen to this:

**Jan:** _My database blew up. Something about corrupt, torn pages and Google can’t save me. John, can you help?_

**John:** _Jan, let’s just restore to the other server so we can figure this out without being on the verge of heart attacks._ 

**jan :** _backups?_

Don’t be that person! Backup your data and back it up as much as you possibly can. Most of all restore it wherever you can to test it, well, can be restored. This is a nice feature to know you have before you need to have it.