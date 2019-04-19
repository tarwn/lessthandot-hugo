---
title: 'Mirroring: Witness misconceptions in High-Performance'
author: Ted Krueger (onpnt)
type: post
date: 2010-02-16T14:22:01+00:00
ID: 704
excerpt: I wanted to start writing a series of blogs on mirroring to share what I've learned 
  over the years and since SQL Server 2005 gave us this feature.  Before we go into that I 
  want to go over the operating modes that we have in mirroring.  We have two operating modes, 
  Asynchronous and synchronous.  The first major key is Asynchronous operating mode is only 
  available in Enterprise Edition (and Developer).
url: /index.php/datamgmt/dbadmin/mirroring-witness-misconceptions-in-high/
views:
  - 9980
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server
tags:
  - high availability
  - mirroring
  - sql server 2008

---
## Concepts in Mirroring

I wanted to start writing a series of blogs on mirroring to share what I've learned over the years since SQL Server 2005 gave us the ability to setup mirroring for High Availabtility (HA). Before we go into that I want to go over the operating modes that we have in mirroring. We truly have two configurations for mirroring, Asynchronous and Synchronous. These are better known as operating modes High Availability, High Performance and High Protection. In my personal daily methods as a DBA, I think we can focus on there essentially being two modes of operation, High Availability and High Performance. High Protection is a sound method of mirroring but typically a setup that does not need to be done. This simply says we are taking a witness out of the picture in mirroring which forces a manual failover in the event of a lose connection to a principal database. In most cases this is due to the thought process that a witness must be a fully licensed and “_hard core_” database server much like the principal and mirror instances. In truth, we can use any edition including SQL Express for a witness so removing the witness in sychronous mirroring isn't very ideal given the addition of transaction latency from this mode. The first major key to go over in Asynchronous mirroring is that this type of mirroring is only available in Enterprise Edition (and Developer). Some misconceptions are that if we remove the witness from the mirroring landscape on Standard Edition, we achieve asynchronous mirroring. This is, however, not true. In this case we are running in Synchronous mode but without automatic failover or safety off. Transactions are still synchronized on both partners first even without the witness. So in short, we still have the added performance hit with synchronous mirroring but without automated failover abilities. To achieve true asynchronous mirroring we need Enterprise and the features it brings along with it. 

The witness allows us to utilize the key aspect of mirroring in helping us protect the data and give us true high database availability (HA). However, the witness is not required and on many occasions omitted from the initial setup of mirroring when running in Synchronous mirroring. Sadly the safety is later found to be a requirement by the business only after the HA strategy is put into effect in the case of a disaster. If you have found that a witness is required after configuring mirroring, a full reconfiguration is not required. All we would need to do is set the witness endpoint up and then alter the principal database to set the witness. 

Some points to ensure are taken care of at the stage of adding a witness

  1. Opening the port on the network and local server
  2. The witness becomes a key role in production and should be treated as such
  3. Anytime a major change to the landscape is done it should be done on lower active times

Books Online (BOL) does a good job in describing operating modes in my opinion. These operating modes can be a bit confusing but there are key points that can be taken into account when setting your HA strategies. I want to try and focus on a few excerpts from BOL and mirroring located on the Mirroring Overview page so help better understand the variables while determining one's own setup. 

## Operating Modes

> _
  
> High-performance (Safety-Off)</p> 
> 
> <span class="MT_smaller">In high-performance mode, as soon as the principal server sends a log record to the mirror server, the principal server sends a confirmation to the client. It does not wait for an acknowledgement from the mirror server. This means that transactions commit without waiting for the mirror server to write the log to disk. Such asynchronous operation enables the principal server to run with minimum transaction latency, at the potential risk of some data loss.</span></i></blockquote> 
> 
> So in the excerpt from BOL above, we can see that we have vulnerability but this can be acceptable given variables like mirroring over a WAN or where performance outweighs availability. The important thing to keep in mind is, even in high-performance one is able to configure the mirroring to add a witness. This is not recommended and holds no real value or failover abilities. Oftentimes it is configured on Asynchronous setups though. I'm not really sure why the option of adding a wtiness to high performance mirroring is allowable. I have also not found a clean and clear answer to actually seeing benefit in leaving the option for a witness on high-performance operating mode. With everything I wrote, I think the best way for us to see this is to do it for a test together. We'll add a witness to a mirror running in high performance and then show a failure and the reaction to the configuration. 
> 
> Let's work on 3 development instances to show this
> 
> Instances are TK2008 (Standard), TK2008DEV (Developer) and TK2008DEV2 (Developer)
> 
> Our two Developer instances have a database “NEEDTOMOVE” mirrored running in high-performance without a witness. 
> 
> We want to get a witness in there now to show how common the misconception is that it will help for automating failover. 
> 
> To do this we first utilize the instance TK2008 as a witness by creating an endpoint on it as follows
> 
> sql
CREATE ENDPOINT Endpoint_Mirroring
>     STATE=STARTED 
>     AS TCP (LISTENER_PORT=7022) 
>     FOR DATABASE_MIRRORING (ROLE=WITNESS)
> GO
```

> Next we verify the port configuration using netstat –a
> 
> <div class="image_block">
>   <img src="/wp-content/uploads/blogs/DataMgmt/mirror_3.gif" alt="" title="" width="628" height="36" />
> </div>
> 
> Now on the principle we alter the database to let it know we want the witness on TK2008:7022 to act as the partner in the relationship to handle failing to the mirror. We do this with the following
> 
> sql
ALTER DATABASE NEEDTOMOVE 
> 	SET WITNESS = 
> 	'TCP://LKFW0133.il.pharmedium.com:7022'
```

> Once this is done we can query <span class="MT_green">sys.database_mirroring</span> to validate our witness is connected even with safety still OFF.
> 
> sql
SELECT 
> 	mirroring_safety_level_desc, 
> 	mirroring_witness_name, 
> 	mirroring_witness_state_desc 
> FROM sys.database_mirroring
> WHERE mirroring_safety_level_desc IS NOT NULL
```
<div class="image_block">
>   <img src="/wp-content/uploads/blogs/DataMgmt/mirror_2.gif" alt="" title="" width="588" height="91" />
> </div>
> 
> Open services.msc and stop TK2008DEV (replaced with your principal). When we go to the mirror we can see from the database that was our mirror didn't actually become the principal but went into recovery.
> 
> <div class="image_block">
>   <img src="/wp-content/uploads/blogs/DataMgmt/mirror_1.gif" alt="" title="" width="269" height="150" />
> </div>
> 
> At this point we may think all that needs to be done is to restore with recovery. However, when doing so, we will receive an error that the database is configured for mirroring and the restore will terminate. To bring this database up we must remove mirroring all together.
> 
> sql
ALTER DATABASE NEEDTOMOVE SET PARTNER OFF
> GO
```

> This brings up another problem in getting our mirroring reconfigured. In the case of Asynchronous mirroring the databases are typically large, high transactional databases that are set geographically apart from each other. This adds hardships to configuring and starting mirroring. Getting the mirror database to the synchronization level enough to start mirroring can truly be an interesting task in itself. 
> 
> To read more on the impact of a witness on mirroring review, “[Asynchronous Database Mirroring (High-Performance Mode)][1]“
  
> 
> 
> > _High-Safety</p> 
> > 
> > <span class="MT_smaller">...the mirror server synchronizes the mirror database together with the principal database as quickly as possible. As soon as the databases are synchronized, a transaction is committed on both partners, at the cost of increased transaction latency</span></i></blockquote> 
> > 
> > This is another key excerpt that I want to focus on. In high-safety we achieve full high availability by incorporating the third witness in the relationship between the partners. This allows for completely automated failover. Ideally we always want to have this configured but there are WAN considerations and reasons why one uses mirroring that could push to a high-performance strategy. 
> > 
> > The most important thing in mirroring in High-Safety I can stress is one has to weigh the importance of securing the data to the performance factor that mirroring at this level brings with it. Local installations rarely need to be anything but synchronous and with safety set to full. Performance tuning should be focused on other areas before considering removing High-Safety. Indexing, Query tuning, IO, Memory and instance configurations are a few starting points to check for increasing performance. Let mirroring do what it does best in keeping the data safe in the event of a local failure or planning downtime on your database servers without creating business downtime. 
> > 
> > ## What's next?
> > 
> > I won't try to fool anyone. There are hundreds of, “Troubleshooting mirroring” blogs and whitepapers out there. What I thought may help a few people would be my own steps in troubleshooting. To do that I am going to follow this blog up with a setup of mirroring and then we'll break/fix it as we work through things.
> > 
> > Until then..protect your data!

 [1]: http://msdn.microsoft.com/en-us/library/ms187110.aspx