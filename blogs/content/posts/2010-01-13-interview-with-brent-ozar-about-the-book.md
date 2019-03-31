---
title: Interview with Brent Ozar about the book Professional SQL Server 2008 Internals and Troubleshooting
author: SQLDenis
type: post
date: 2010-01-13T19:01:27+00:00
url: /index.php/datamgmt/dbprogramming/interview-with-brent-ozar-about-the-book/
views:
  - 5672
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - book
  - interview
  - performance tuning
  - sql server 2008

---
I noticed Brent Ozar worked on a SQL book titled: [Professional SQL Server 2008 Internals and Troubleshooting][1]. This book is now available and I decided to ask Brent some questions about this book. This interview was conducted by email, enjoy.

**Denis: Is the book geared towards a beginner/intermediate level user or do you have to be an advanced user to really utilize the information in this book?**

I like to think that the reader is somebody who&#8217;s been managing SQL Servers for a year or two and is getting frustrated. He doesn&#8217;t understand what&#8217;s going on inside the engine when a query runs. He&#8217;s not satisfied with just creating more indexes or running sp_who2 &#8211; he wants to know what&#8217;s actually happening under the hood. He can&#8217;t afford top-notch consultants, or he can&#8217;t get good ones locally, or he wants to BECOME a top-notch consultant.

This book covers so many gray areas that traditional books haven&#8217;t covered. CPUs matter. Memory matters. Storage matters. Latching matters. Traditional SQL books haven&#8217;t dived deeply enough into these subjects, and I think this book does.

**Denis: What are the most important things a person can do to master SQL troubleshooting?**

Don&#8217;t change anything you don&#8217;t fully understand, and don&#8217;t take anybody&#8217;s word for anything. Just because you read a recommendation in a forum that your server will go faster when MAXDOP is set to 1 doesn&#8217;t mean it&#8217;s going to work in your individual situation. Just because MAXDOP = 1 makes one of your servers faster doesn&#8217;t mean it&#8217;ll make them all faster. When you change settings, you change the way SQL Server behaves, and you should understand the ramifications.

One of the first things I do when I&#8217;m troubleshooting a server is find out all of the non-default settings. Don&#8217;t assume AUTO_CLOSE is turned off &#8211; people do crazy things. I make a list of everything that isn&#8217;t set at factory defaults, and I start asking why. Don&#8217;t get me wrong &#8211; there&#8217;s some easy knobs to turn in order to make SQL Server go faster, like Instant File Initialization, but there&#8217;s not a lot of surefire tweaks that work in every single situation.

**Denis: With the addition of Dynamic Management Views is it now easier to find bottlenecks than before?**

Without a doubt. Whenever I present on DMVs, I hear a chorus of questions asking how to get that same information in SQL Server 2000. DMVs are without a doubt the most helpful thing in my troubleshooting and tuning. Managing a SQL Server without querying the DMVs is like trying to bake a cake without knowing how the oven temperature control works. Your results will definitely vary from cake to cake.

**Denis: What is your favorite Dynamic Management View to troubleshoot performance?**

sys.dm\_db\_missing\_index\_details, I wish I could quit you.

I do &#8220;weekend&#8221; performance tuning on the side, so when I troubleshoot performance, I&#8217;m usually looking at a database I&#8217;ve never seen before. Indexing is tough to do right, and easy to do wrong. I can usually get huge performance improvements by tweaking the index strategy. The missing index DMVs give me a starting point without having to run traces. They&#8217;re still not a silver bullet &#8211; they&#8217;re like the Index Tuning WIzard or Database Tuning Advisor in that they can give some pretty nasty advice. When used properly, though, they can give huge performance boosts. 

**Denis: Is there a place for Solid State Drives, perhaps for tempdb or is it still too early for mission critical servers?**

Last year when I spoke about performance tuning at a Quest event in California, there were already several folks there using SSD drives packaged in PCI Express cards like Fusion-io, and they were using it for more than just TempDB too. When you&#8217;re really desperate for performance, like when your app just won&#8217;t scale and you&#8217;re out of space for more SAN gear, you get desperate, and you&#8217;ll try those cutting-edge technologies before the rest of us. Those people are out there already, dancing on the edge, and I&#8217;m hearing good reports back.

I think it&#8217;s a bad idea for mission critical servers because truly mission critical databases should be on clusters, and most SANs don&#8217;t take solid state drives yet. I think the best use for SSDs in SANs is for automated tiering systems where the SAN controller automatically moves data around based on how often it&#8217;s accessed. Data that&#8217;s very, very frequently read can get migrated onto SSD. That&#8217;s still a high-end SAN feature though, not mainstream.

**Denis: Should we be looking at Page Life Expectancy or Buffer Cache Hit Ratio and why?**

We should be looking at Page Life Expectancy, but only in combination with all of the metrics packaged together. I talked about this in my blog post, Bottlenecks and Bank Balances:

http://www.brentozar.com/archive/2009/10/bottlenecks-and-bank-balances/

At the end of the day, does it really matter whether your Page Life Expectancy is 70 or 700 or 7000? Is that your biggest bottleneck, and are the application users satisfied with performance? I&#8217;ve managed servers with abysmal Page Life Expectancy numbers (like below 60) that the business was completely satisfied with. Some applications just aren&#8217;t important or aren&#8217;t worth spending extra money/time/resources on.

**Denis: What part of SQL troubleshooting do people struggle the most with?**

Reading execution plans and doing something about it. I bought Grant Fritchey&#8217;s book, SQL Server 2008 Query Performance Tuning Distilled, for that exact reason &#8211; I just couldn&#8217;t read the plans well enough, and the question comes up so gosh-darned often. I&#8217;m working with a company now that sends me execution plans so large that I would need to print them out on wallpaper and paste it up in a conference room. There comes a point where you have to draw the line and say, &#8220;It&#8217;s not a matter of tuning this query &#8211; it&#8217;s a matter of dumping this query and asking what the user is really trying to accomplish, and how we can meet their needs.&#8221;

**Denis: Can you name some tools that will help with troubleshooting performance?
  
** 
  
I work for Quest Software, but even if I didn&#8217;t, I&#8217;d name-drop Foglight Performance Analysis. It samples SQL Server&#8217;s memory space, captures performance data without requiring a trace, and gives you a gorgeous slice & dice user interface to quickly find the nastiest queries, applications, and users. There&#8217;s flat-out nothing like it on the market.

I couldn&#8217;t imagine performance tuning without Bill Graziano&#8217;s fantastic utility ClearTrace. If you don&#8217;t have Foglight PA, you&#8217;ll be running traces to capture queries, and ClearTrace really helps you make sense of trace file contents. I can fire up ClearTrace and find problem queries very fast.

In the book, I wrote the Performance Dashboard chapter, and I&#8217;d highly recommend that for anybody who has zero budget for SQL Server tools. It&#8217;s not perfect &#8211; it&#8217;s far from it &#8211; but it&#8217;s one heck of a big leap forward when you&#8217;re stumbling around in the dark trying to find bottlenecks.

**Denis: What are some of the biggest mistakes people make when setting up a new server in terms of performance?** 

Not using Instant File Initialization, not aligning their partitions, and remote desktopping into the server to run things like SQL Server Management Studio. STOP remote controlling your servers. SSMS burns up memory that should be dedicated to the database engine. I&#8217;ve got a setup checklist on my site, too:

http://www.brentozar.com/archive/2008/03/sql-server-2005-setup-checklist-part-1-before-the-install/

**Denis: Which chapter was the hardest to write and can you explain why?
  
Both of them.**

Lemme make one thing perfectly clear: I am the dumbest author involved in this book. For real. When I first got the chance to be involved with this book, I looked at the available chapter list and said, &#8220;I&#8217;ll take storage and the Performance Dashboard.&#8221; I took storage because I loooooove storage, and I took the Performance Dashboard because I thought it would be easy.

The storage chapter was hard because I wanted to keep working on it forever. One of my coauthors said he spent 1,200 hours on one chapter, and I can see how that would happen. As I was writing and editing the storage chapter, I just kept thinking of more and more things I wanted to throw in there, but when you&#8217;re writing, you have to make sure you&#8217;re covering each subject well enough. I didn&#8217;t want to do a half-ass job covering a million topics. I eventually had to cut myself off to do my day job, and I&#8217;m still killing myself over things I didn&#8217;t get to include. I didn&#8217;t include the infamous 15-second IO warning events, for example. I&#8217;m hoping we get to do a SQL 2008 R2 version so I can expand it.

The Performance Dashboard chapter was hard because I wrote it and then said, &#8220;Okay, this is not nearly enough information.&#8221; I dove into creating new reports because really, if you&#8217;re going to go down the Performance Dashboard route, you&#8217;re going to want to roll your own stuff too. I didn&#8217;t originally sign onto the project to write an SSRS book (because I&#8217;m an SSRS noob) but I had to include some stuff on it, and that made me nervous.

**Denis: Will you write another book?**
  
Originally I said no. I yelled no from the mountaintops. Now, wouldn&#8217;t you know it, I&#8217;m finishing an ebook as we speak. This one&#8217;s on Twitter though. I don&#8217;t think I&#8217;ll do another ground-up from-scratch technical book. I have amazing respect for guys like Ross Mistry who keep banging out books.

**Denis: What SQL books are on your bookshelf?**

SQL Server Query Performance Tuning Distilled by Grant Fritchey
  
SQL Server 2005 Performance Tuning by Steven Wort, Christian Bolton, Justin Langford, and more
  
Inside SQL Server 2008 T-SQL Querying by Itzik Ben Gan and others
  
Oracle and VMware by Bert Scalzo
  
The Wine Trials 2010 by Robin Goldstein and Alexis Herschkowitsch (databases and drinks go together like developers and cursors)
  
And tons more, but that&#8217;s a good start.

**Denis: Who are your favorite authors?**

I really like reading James Rowland-Jones&#8217; chapter on Latching in our book. He has a really good sense of humor that plays well in print.

I like that Kevin Kline writes like he talks &#8211; he makes complex topics deceptively simple. Anybody can sandblast you with fifty-cent words, but Kevin distills things down in a way that&#8217;s easy to read quickly.

Tim Ford spends entirely too much time writing his next book and needs to blog more, because he&#8217;s hilarious, and I&#8217;m going through withdrawal.

My favorite authors of all time, though, would be Dave Eggers, Douglas Adams, and Garrison Keillor &#8211; and the poet W. H. Auden for good measure. I&#8217;m no Paul Randal &#8211; I think I read maybe 10 books in 2009, and I don&#8217;t see that changing in 2010.

**Denis: How are you involved with SQLServerpedia?**

There&#8217;s only two of us really heavily involved on a day-to-day basis. Brett Epps handles everything technical &#8211; if it involves PHP, graphics, MySQL, anything like that, that&#8217;s his full time job. (I&#8217;m thankful for him, because when I was the guy doing that, the site looked bad and performed worse.) I handle everything else &#8211; working with the bloggers, checking article submissions, planning promotions, coming up with what we&#8217;re going to do next. I get a lot of logistical support from a bunch of Questies &#8211; Christian Hasker, Andy Grant, Betsy Mendenhall, Heather Eichman, and Stephanie McCulloch &#8211; but most of the work is still me.

I love it. I really wish I had more hours in the day to spend on SQLServerPedia. We&#8217;ve got a million cool ideas, and it just comes down to manpower.

**Denis: Where can we expect to see you in 2010? Any conferences, seminars, trade shows or classrooms perhaps?**

EVERYWHERE, MUHAHAHA. Okay, maybe not. I&#8217;ll be at the WorkTamer conferences in Canada, the Microsoft MVP Summit in Seattle, SQLSaturday Chicago, the PASS Summit in Seattle, and maybe the European one as well. I&#8217;m aiming for the Microsoft Certified Master program in March, and if that happens, I&#8217;ll be off the radar for a few weeks.

I&#8217;ve got a day-long free virtual event coming in February with Kevin Kline and Ari Weil, too &#8211; we&#8217;ll be talking DMVs all day long. More news soon&#8230;

That is it, thanks to Brent for the interview, you can visit Brent&#8217;s site here: http://www.brentozar.com/ and you can follow him on twitter here: http://twitter.com/brento

To check out the book, visit the wrox site here: http://www.wrox.com/WileyCDA/WroxTitle/Professional-SQL-Server-2008-Internals-and-Troubleshooting.productCd-0470484284.html or Amazon here: [Professional SQL Server 2008 Internals and Troubleshooting][1]

 [1]: http://www.amazon.com/gp/product/0470484284?ie=UTF8&tag=sql08-20&linkCode=as2&camp=1789&creative=390957&creativeASIN=0470484284