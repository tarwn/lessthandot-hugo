---
title: 'Distributed Storage: How SQL Azure Replicas Work'
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2015-11-18T14:36:12+00:00
ID: 4228
url: /index.php/architect/distributed-storage-how-sql-azure-replicas-work/
featured_image: /wp-content/uploads/2015/11/SQL-Azure-Sq.png
views:
  - 9034
rp4wp_auto_linked:
  - 1
categories:
  - Architecture, Design and Strategy
  - Azure
  - Data Management
tags:
  - distributed systems
  - sql azure

---
One of the benefits of Microsoft SQL Azure over an on-premises or VM installation is built-in resiliency. In a typical on-premises/VM installation your database lives on a single server, with all the single points of failure that brings to mind. SQL Azure, on the other hand, always has 3 or more replicas assigned for each database. This allows it to weather issues like network glitches and commodity hardware failures with no administration and little to no downtime.

<div style="margin: 1em 3em; padding: 2em; font-size: 110%; border: 1px solid #dddddd; background-color: #eeeeee; border-radius: 6px;">
  There are interactive simulations below! Skip ahead if you just want to play with them, read through if you want more of the details 🙂
</div>

Finding good, detailed articles about this has been difficult. Here's a couple I found:

  * [Fault-tolerance in Windows Azure SQL Database][1]
  * [Inside Microsoft Azure SQL Database][2] (retired article – this is a revision from prior to it being taken down)

What really interested me was the database communications. How do reads flow into the database when there's 3 of them? How do writes occur when one of my database replicas is down? How does a replica catch back up when it is available again?

I learn well from reading, but had to reread the articles a few times over the years before the information really stuck. So this post is an attempt to approach the topic from another direction, with active simulations of how this communications works in SQL Azure.

_Note: When the two disagree, I'll rely on the slight less out of date top article. When my practical (aka, support tickets) experience disagrees with both, I'll point it out._

_Note 2: I suspect the simulations below will make this a mobile-unfriendly post, sorry._

# Key Details of SQL Azure

Before we start answering the questions above, lets extract some details from those dense articles to set the stage. 

There are actually several layers of systems involved in SQL Azure, this post is going to focus just on the database operations. I'll point out when the “fabric” is involved, but it won't be part of the simulations. That being said, here's the key details for the database:

  * There are a minimum of 3 database replicas at all times
  * All incoming traffic goes to the Primary replica (elected by the “fabric”)
  * Replicas exist on different physical servers (created/managed by the “fabric”)
  * Database Writes require a quorum of 2 of the 3 replicas acknowledging the write in order to COMMIT
  * Database Reads return directly from the Primary replica
  * There is support for both transactional and full restores

Each “data node” in the network includes the SQL Server processes involved in the items above as well asservices for failure detection, re-establishing nodes after failure, throttling, and so on. I won't be diving into those today, this is all about the database replica.

Some Warnings:

  * As far as I know, Azure SQL does not use HTTP codes internally. I used them in the simulations below as I thought they would be more recognizable then me making something up
  * SQL Server is not limited to key/value or single statement operations, this is a simplification I made so I could focus on the mechanics of the communications instead of diving into the MSSQL storage engine

## How do writes work?

Writes in SQL Azure come through a TDS gateway that, transparent to us, passes our queries to the Primary replica. The replica determines what the change will be from our operation, assigns a Change Sequence Number (CSN) to it, then replicates it to the secondary replicas. The Primary replica only commits the changes after it has received at least one acknowledgement back from the secondaries, ensuring the data now is now on at least two replicas (the Primary and one secondary). 

<div style="margin: 1em 3em; padding: 1em; text-align: center; border: 1px solid #dddddd; background-color: #eeeeee; border-radius: 6px;">
  Press the “Run” button below to start sending writes from the “gateway” into the replicas.
</div>

<iframe src="http://tarwn.github.io/DistributedSamples/Javascript/blog/azure_w.html" style="width: 800px; height: 600px; border: 2px solid #eeeeee"></iframe>

What you're seeing is a simulation of the writes I described above. Each replica has a set of data that has been stored and a short transaction log and indicates whether it is the “PRIMARY” or “secondary” in it's title bar. 

The “gateway” in the top left sends each write to the PRIMARY replica. The PRIMARY replica calculates the storage change of the write, assigns it a CSN, and sends it to the two secondary replicas. These secondaries apply the change locally and send back an acknowledgement, at which point the PRIMARY commits the change (more on this in a moment). Once the PRIMARY commits the change, it returns a success response back to the person that sent that particular INSERT or UPDATE statement.

Keep in mind, this is a simulation. The model for the COMMIT above is based on what I found in the articles above, but is probably not quite right (and I would love it if someone has more definitive information about this so i could improve it).

## How do reads work?

Reads are easy. Since the TDS gateway directs all queries to the Primary replica and it always has the most up to date data, it can respond with the values it has locally without seeking a quorum from the other replicas. 

<div style="margin: 1em 3em; padding: 1em; text-align: center; border: 1px solid #dddddd; background-color: #eeeeee; border-radius: 6px;">
  Press the “Run” button to send some quick writes and then watch how reads work.
</div>

<iframe src="http://tarwn.github.io/DistributedSamples/Javascript/blog/azure_r.html" style="width: 800px; height: 600px; border: 2px solid #eeeeee"></iframe>

As “Read” messages come in from the gateway, the PRIMARY replica looks the value up locally and returns it directly. 

In the real SQL Azure replicas, this means that the PRIMARY replica has more work to do then the secondaries. This is where the “fabric” behind the scenes becomes critical, as it is responsible for trying to maintain a good balance of primary (read and write load) and secondaries (writes) across each server. When a new replica is created or a new PRIMARY is elected from the existing replicas, the “fabric” has to adjust things behind the scenes to balance out the work.

## Weathering Outages

The point of the 3 node replica setup is to get high levels of resiliency from shared commodity hardware. If an outage is short enough, a transaction log update from whichever replica has the latest log can catch a restoring replica up to date. If the log has been exhausted, a full update can catch up a replica. Eventually, if the server or replica is unavailable long enough, the fabric will provision a new replica to replace it (not implemented in the simulation).

To help show both short outage cases, the simulated replicas only keep their last 4 transactions. This way a replica missing only a couple transactions will restore from transactions but a replica offline for more than 4 transactions will require a full restore.

<div style="margin: 1em 3em; padding: 1em; text-align: center; border: 1px solid #dddddd; background-color: #eeeeee; border-radius: 6px;">
  Press the “Run” button to watch a shorter and longer outage while writing.
</div>

<iframe src="http://tarwn.github.io/DistributedSamples/Javascript/blog/azure_o.html" style="width: 800px; height: 600px; border: 2px solid #eeeeee"></iframe>

This is running a scripted loop of operations to show both restore cases. The script presses the turbo button during write transactions so we can skip ahead to the restore operations. When a replica's border turns red, this means it has gone offline.

1) We prime the network with a couple writes, take replica “B” offline, send a couple more writes, then bring replica “B” back online. This results in a restore from transaction log.
  
2) After a couple more writes, we take replica “B” offline again, wait for 5 more writes to occur, then bring replica “B” back online. This results in a full restore.

When a replica comes online, it sends a restore request to the other replicas and identifies the latest CSN it applied. If the other replicas have that CSN in their log, they send back the log and the restoring replica can use the latest of those two logs to catch up. if neither of the replicas can send back a log, then the restoring replica asks for a full restore. This isn't heavily detailed in the documentation, so this is another place that matches the document but may not quite match the reality.

When the PRIMARY replica goes down, the documentation outlines monitoring that occurs that causes the “fabric” to elect a new PRIMARY replica. From my own experience, one or more types of failures are actually monitored on a 5-10 minute poll and this will result in a short outage (remainder of the 5-10 minute poll loop) before it is noticed and the “fabric” elects a new PRIMARY from the remaining secondaries.

For longer replica outages, not included in this simulation, the “fabric” will provision a new replica from a full restore and add it to the cluster as a new secondary, replacing the bad node. 

Now that we have Writes, Reads, and an IT Person stumbling over power cords, it's time to put it all together and play a little.

## Putting it all together

Here we have a functioning network and 3 buttons. One button starts a stream of random reads and writes, the next unleashes our stumbling IT person to wander aimlessly around and stumble over power cords, and the third allows you to toggle between slower and faster message travel times.

<div style="margin: 1em 3em; padding: 1em; text-align: center; border: 1px solid #dddddd; background-color: #eeeeee; border-radius: 6px;">
  Start sending random writes, reads, and outages!
</div>

<iframe src="http://tarwn.github.io/DistributedSamples/Javascript/blog/azure_i.html" style="width: 800px; height: 600px; border: 2px solid #eeeeee"></iframe>

One thing you may notice is that the outages are no longer confined to a single replica, now even the primary can go down.

There is a more extensive example here: [Azure SQL Simulation Console][3]

This adds tracking expected versus actual responses, stale data, outage stats, and SLAs as well as the ability to add additional replicas to the cluster.

## Where the Simulation Is Wrong(ish)

There are a few things that either did not match reality or for which I couldn't find good enough information. Feedback would be awesome for these. There are also a few places where I simplified concepts that were outside the scope of talking about the communications and restore processes, if their absence is a problem, let me know and I'll try to extend the models.

Things I simplified:

  * Monitoring: I didn't model the fabric or neighbor-based monitoring, instead servers will magically come back online every time and monitoring is performed by the generic “network” simulation.
  * Writes/Commits: I simplified this to single insert commits
  * HTTP Error Messages: I used HTTP status codes in messages because I don't know the internal communications and it seemed good/simple enough

Things I got wrong:

  * Commits: While I tried to match the explained process, it is not wholly accurate and there is definitely a bug when a commit comes in with an Online Primary, a restoring Secondary, and an Offline secondary. It will be queued up for commit on the secondary but aborted on the primary due to lack of quorum, leading to a secondary that has bad data (and possibly both secondaries, if the other comes online before the next write occurs).
  * The Full Restore logic – this was an extrapolated guess from the documentation
  * SQL Transactions and multi-step operations – these aren't implemented purely, but didn't seem to add much value from the perspective of showing how the distributed logic works

See anything else? I would love to know so I could improve the models, let me know.

 [1]: https://azure.microsoft.com/en-us/blog/fault-tolerance-in-windows-azure-sql-database/
 [2]: http://social.technet.microsoft.com/wiki/contents/articles/1695.inside-microsoft-azure-sql-database/revision/33.aspx
 [3]: http://tarwn.github.io/DistributedSamples/Javascript/azureSql.html