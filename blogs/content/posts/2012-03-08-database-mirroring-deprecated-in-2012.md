---
title: Database Mirroring Deprecated in 2012
author: Ted Krueger (onpnt)
type: post
date: 2012-03-08T12:22:00+00:00
excerpt: 'As the news travels about the Deprecated Database Engine Features in SQL Server 2012, many start to get the fainting scare in them due to features they have worked endlessly on to provide a stable solution in either their company’s system or client’s sy&hellip;'
url: /index.php/datamgmt/dbprogramming/mssqlserver/database-mirroring-deprecated-in-2012/
views:
  - 12449
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server

---
As the news travels about the [Deprecated Database Engine Features in SQL Server 2012][1], many start to get the fainting scare in them due to features they have worked endlessly on to provide a stable solution in either their company’s system or client’s systems.  Reviewing the deprecated list that is generated with each major release of SQL Server is an absolutely critical stage as a SQL Server Professional.  It provides us with that scare that ensures we understand what we will have to do between now and the two major releases into the future.  This is computing and computing evolves with time.  Each evolution may not be completely the right direction but we have little control over that in the long run.  Or do we?

**Database Mirroring as a solution**

Since SQL Server 2005 was introduced and replaced the awful taste that was left by SQL Server 2000, I’ve taken database mirroring very seriously.  It has proven to be a stable solution for HA when none existed before in a native SQL Server world.  The cost was extremely minimal and the need for clustering physical servers was almost completely removed from the equation.  Now, don’t take that as me recommending you never _mirror_ physical servers.  Database mirroring in SQL Server provided the ability to mirror data.  That meant we could have physical servers on their own little island and act upon failovers with our own functional means of a stable failover event.  Let’s face it.  In half of the systems out there, a failover doesn’t mean they will function right.  Even Great Plains, a Microsoft product, has major issues in failing over.  Half of the time, ODBC intervention caused application interfaces to die slowly when a failover occurred.  In the database mirroring world, the failover event can be caught, scripts run and failovers managed though.

AlwaysOn takes the throne now.  Yes, there can be the same principles applied to AlwaysOn.  However, is it a level playing field feature when it comes to cost and operating modes?  Although that is outside the scope of what I am about to get to, rest assured, it is a touchy topic.

When I reviewed the deprecated list and saw that MS finally said whether mirroring was deprecated (they did and it is) I was overcome by a bit of frustration.  That frustration came from this line.

_“If your edition of SQL Server does not support AlwaysOn Availability Groups, use log shipping”_

You may ask why that line bothers me so much.  Log shipping in no way, shape, or form is a High Availability feature.  I am OK with the deprecation of database mirroring to a point.  That point is when the cost starts to force the inability of upgrades and overall more stable solutions.  As it is now, AlwaysOn is an Enterprise only solution.  We, as SQL Server professionals, were jumping for joy when database mirroring was made available in Standard edition.  There was a solid solution for high availability for the lower needed spending on licensing of the standard edition.  The other issue we have to take into account is the massive change to licensing in SQL Server 2012.  Essentially, SQL Server allowed us to license to the physical CPU up until SQL Server 2012.  Now, logical CPU licensing has taken effect.  Don’t get me wrong.  Everyone is licensing this way and it really didn’t bother me that it was changed.  Yes, I’ll have a hard time selling the upgrade for this one major fact but that is our jobs; show why SQL Server is as good or a better choice and worth the cost.  Can everyone afford that cost?  No, which brings me back to log shipping.

**_Log Shipping is NOT high availability_**.  I truly hope that the same events that came with database mirroring come with AlwaysOn.  A solution that is provided in the standard edition that gives companies a reason to keep upgrading and using SQL Server because they are spending the money in the right areas.

**Conclusion**

I see SQL Server customers not upgrading and even going to other solutions in the future over these changes to licensing and features like database mirroring.  All we ask for is the same great ability to maintain a true HA environment for our clients that cannot afford the price tag of Enterprise editions.

Just to say it again: Log shipping is NOT a high availability feature.  I hope they even take that statement out of the deprecated page.  Every time I see it, it frustrates me because I can picture all of the failovers that wait ten minutes to come online while they restore that lost log or try to figure out how to recover the massive log backup that holds vital data.  High availability doesn’t allow of the comfort of losing any data and failovers that have any interruptions to the business.

 [1]: http://msdn.microsoft.com/en-us/library/ms143729%28v=sql.110%29.aspx