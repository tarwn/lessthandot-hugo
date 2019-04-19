---
title: Microsoft Enterprise Developer Conference Overview
author: SQLDenis
type: post
date: 2009-05-07T11:13:16+00:00
ID: 416
excerpt: 'This is the second time that I went to the Enterprise Developer Conference. Due to the downturn Microsoft merged the financial and health/live sciences conferences into one. Last years main focus was High Computing, this years main focus was......tada..&hellip;'
url: /index.php/enterprisedev/appserver/microsoft-enterprise-developer-conferenc/
views:
  - 4256
rp4wp_auto_linked:
  - 1
categories:
  - Application Server
tags:
  - azure
  - cloud computing
  - sql server data services
  - ssds

---
This is the second time that I went to the Enterprise Developer Conference. Due to the downturn Microsoft merged the financial and health/live sciences conferences into one. Last years main focus was High Computing, this years main focus was......tada.....drumroll.....Cloud Computing. No big surprise of course. There were always 5 sessions at the same time so I can only report on the ones that I attended. The SQL Server Data Services session was in my opinion the best one but then again I am biased. You can actually watch all the session at the following site [entdevcon.com][1] Right now only the day 1 sessions are up there

**The Keynote**
  
The keynote was all about Azure and Cloud Services. Explained was the roadmap that led to Azure, what Azure is and where it is going. I have to agree for the most part that it really doesn't make a whole lot of sense to manage your own infrastructure. Have it done by someone who can provide this with a nice SLA and you have a lot less headaches and costs

**Azure**
  
What is this Azure thing and how do you pronounce it? Azure is nothing else but hosted applications, instead of deploying to a server you deploy to the cloud. Developing Azure apps compared to traditional apps is very similar. It will be interesting how many apps will end up in the cloud, if you want to get rid of some of those machines that have one app on them that the whole department uses then this is a great way to consolidate. It all depends on pricing of course but it would be very strange if Azure turns out to be more expensive

**SQL Server Data Services**
  
SQL Server in the cloud completely changed from the first version, while in the beginning it was just value pair key data, the version now is completely relational. On the picture that I snapped at the session you can see what is implemented and what is not. So if you have been using CLR in SQL Server or are using data types that are .NET data types like georaphy and geometry spatial data types then you are out of luck. Microsoft analyzed all the data from the customers who are in the Beta program and found out that 90% of them had a database that was less than 2GB. This is also the reason that they will cap it at 10GB per database. Microsoft also noticed that 95% of the apps that were in the beta program would run without a problem on SSDS by just switching the connection string to point to the cloud

You will get special DMVs that will tell you how large your database is, how much bandwith you have used so far etc. All other DMVs are disabled as well as sp_who and similar procs since your database might be on the same server as other customers databases.

The data will be replicated across different data centers and perhaps across continents as well. You can specify in your SLA that your data has to be stored in the US for example, in that case it will be in multiple data centers in the US

Your SLA will also determine downtime and if you want the data to be replicated, the cheapest plan will presumably not include geo replication etc

Pricing for SSDS will be announced in July so then you can figure out if it make economic sense for you to move the database in the cloud

If you are just using plain SQL and everything that is supported by SSDS then all you have to do is change the severname in your connection string for it to work, even SQL Server Management Studio just works. That said you can not look at execution plans or do a SET SHOWPLAN TEXT which is a bummer. How will you performance tune your query?
  
Below is a picture showing all the things that are available and not available in the V1 release
  
[<img src="http://farm4.static.flickr.com/3645/3509561219_61a9e5d3ac.jpg" width="500" height="388" alt="SQL Server Data Services Compatibility" />][2]

Watch the presentation here: [SQL Data Services:A Relational Database in the Cloud-Microsoft][3]

**Surface**
  
There were 2 surface tables on display both Infusion and 49Labs had one. I took a short video of a card game so that you can see how it looks, if you actually bang on the chips with your hand they pop up just like in real life.

 [1]: http://entdevcon.telligent.com/sessions/
 [2]: http://www.flickr.com/photos/denisgobo/3509561219/ "SQL Server Data Services Compatibility by Denis Gobo, on Flickr"
 [3]: http://entdevcon.istreamplanet.com/video.asp?v=36