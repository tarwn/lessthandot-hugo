---
title: Interview with Brent Ozar about the book Professional SQL Server 2008 Internals and Troubleshooting
author: SQLDenis
type: post
date: 2010-01-13T19:01:27+00:00
ID: 674
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

I like to think that the reader is somebody who's been managing SQL Servers for a year or two and is getting frustrated. He doesn't understand what's going on inside the engine when a query runs. He's not satisfied with just creating more indexes or running sp_who2 &#8211; he wants to know what's actually happening under the hood. He can't afford top-notch consultants, or he can't get good ones locally, or he wants to BECOME a top-notch consultant.

This book covers so many gray areas that traditional books haven't covered. CPUs matter. Memory matters. Storage matters. Latching matters. Traditional SQL books haven't dived deeply enough into these subjects, and I think this book does.

**Denis: What are the most important things a person can do to master SQL troubleshooting?**

Don't change anything you don't fully understand, and don't take anybody's word for anything. Just because you read a recommendation in a forum that your server will go faster when MAXDOP is set to 1 doesn't mean it's going to work in your individual situation. Just because MAXDOP = 1 makes one of your servers faster doesn't mean it'll make them all faster. When you change settings, you change the way SQL Server behaves, and you should understand the ramifications.

One of the first things I do when I'm troubleshooting a server is find out all of the non-default settings. Don't assume AUTO_CLOSE is turned off &#8211; people do crazy things. I make a list of everything that isn't set at factory defaults, and I start asking why. Don't get me wrong &#8211; there's some easy knobs to turn in order to make SQL Server go faster, like Instant File Initialization, but there's not a lot of surefire tweaks that work in every single situation.

**Denis: With the addition of Dynamic Management Views is it now easier to find bottlenecks than before?**

Without a doubt. Whenever I present on DMVs, I hear a chorus of questions asking how to get that same information in SQL Server 2000. DMVs are without a doubt the most helpful thing in my troubleshooting and tuning. Managing a SQL Server without querying the DMVs is like trying to bake a cake without knowing how the oven temperature control works. Your results will definitely vary from cake to cake.

**Denis: What is your favorite Dynamic Management View to troubleshoot performance?**

sys.dm\_db\_missing\_index\_details, I wish I could quit you.

I do “weekend” performance tuning on the side, so when I troubleshoot performance, I'm usually looking at a database I've never seen before. Indexing is tough to do right, and easy to do wrong. I can usually get huge performance improvements by tweaking the index strategy. The missing index DMVs give me a starting point without having to run traces. They're still not a silver bullet &#8211; they're like the Index Tuning WIzard or Database Tuning Advisor in that they can give some pretty nasty advice. When used properly, though, they can give huge performance boosts. 

**Denis: Is there a place for Solid State Drives, perhaps for tempdb or is it still too early for mission critical servers?**

Last year when I spoke about performance tuning at a Quest event in California, there were already several folks there using SSD drives packaged in PCI Express cards like Fusion-io, and they were using it for more than just TempDB too. When you're really desperate for performance, like when your app just won't scale and you're out of space for more SAN gear, you get desperate, and you'll try those cutting-edge technologies before the rest of us. Those people are out there already, dancing on the edge, and I'm hearing good reports back.

I think it's a bad idea for mission critical servers because truly mission critical databases should be on clusters, and most SANs don't take solid state drives yet. I think the best use for SSDs in SANs is for automated tiering systems where the SAN controller automatically moves data around based on how often it's accessed. Data that's very, very frequently read can get migrated onto SSD. That's still a high-end SAN feature though, not mainstream.

**Denis: Should we be looking at Page Life Expectancy or Buffer Cache Hit Ratio and why?**

We should be looking at Page Life Expectancy, but only in combination with all of the metrics packaged together. I talked about this in my blog post, Bottlenecks and Bank Balances:

http://www.brentozar.com/archive/2009/10/bottlenecks-and-bank-balances/

At the end of the day, does it really matter whether your Page Life Expectancy is 70 or 700 or 7000? Is that your biggest bottleneck, and are the application users satisfied with performance? I've managed servers with abysmal Page Life Expectancy numbers (like below 60) that the business was completely satisfied with. Some applications just aren't important or aren't worth spending extra money/time/resources on.

**Denis: What part of SQL troubleshooting do people struggle the most with?**

Reading execution plans and doing something about it. I bought Grant Fritchey's book, SQL Server 2008 Query Performance Tuning Distilled, for that exact reason &#8211; I just couldn't read the plans well enough, and the question comes up so gosh-darned often. I'm working with a company now that sends me execution plans so large that I would need to print them out on wallpaper and paste it up in a conference room. There comes a point where you have to draw the line and say, “It's not a matter of tuning this query &#8211; it's a matter of dumping this query and asking what the user is really trying to accomplish, and how we can meet their needs.”

**Denis: Can you name some tools that will help with troubleshooting performance?
  
** 
  
I work for Quest Software, but even if I didn't, I'd name-drop Foglight Performance Analysis. It samples SQL Server's memory space, captures performance data without requiring a trace, and gives you a gorgeous slice & dice user interface to quickly find the nastiest queries, applications, and users. There's flat-out nothing like it on the market.

I couldn't imagine performance tuning without Bill Graziano's fantastic utility ClearTrace. If you don't have Foglight PA, you'll be running traces to capture queries, and ClearTrace really helps you make sense of trace file contents. I can fire up ClearTrace and find problem queries very fast.

In the book, I wrote the Performance Dashboard chapter, and I'd highly recommend that for anybody who has zero budget for SQL Server tools. It's not perfect &#8211; it's far from it &#8211; but it's one heck of a big leap forward when you're stumbling around in the dark trying to find bottlenecks.

**Denis: What are some of the biggest mistakes people make when setting up a new server in terms of performance?** 

Not using Instant File Initialization, not aligning their partitions, and remote desktopping into the server to run things like SQL Server Management Studio. STOP remote controlling your servers. SSMS burns up memory that should be dedicated to the database engine. I've got a setup checklist on my site, too:

http://www.brentozar.com/archive/2008/03/sql-server-2005-setup-checklist-part-1-before-the-install/

**Denis: Which chapter was the hardest to write and can you explain why?
  
Both of them.**

Lemme make one thing perfectly clear: I am the dumbest author involved in this book. For real. When I first got the chance to be involved with this book, I looked at the available chapter list and said, “I'll take storage and the Performance Dashboard.” I took storage because I loooooove storage, and I took the Performance Dashboard because I thought it would be easy.

The storage chapter was hard because I wanted to keep working on it forever. One of my coauthors said he spent 1,200 hours on one chapter, and I can see how that would happen. As I was writing and editing the storage chapter, I just kept thinking of more and more things I wanted to throw in there, but when you're writing, you have to make sure you're covering each subject well enough. I didn't want to do a half-ass job covering a million topics. I eventually had to cut myself off to do my day job, and I'm still killing myself over things I didn't get to include. I didn't include the infamous 15-second IO warning events, for example. I'm hoping we get to do a SQL 2008 R2 version so I can expand it.

The Performance Dashboard chapter was hard because I wrote it and then said, “Okay, this is not nearly enough information.” I dove into creating new reports because really, if you're going to go down the Performance Dashboard route, you're going to want to roll your own stuff too. I didn't originally sign onto the project to write an SSRS book (because I'm an SSRS noob) but I had to include some stuff on it, and that made me nervous.

**Denis: Will you write another book?**
  
Originally I said no. I yelled no from the mountaintops. Now, wouldn't you know it, I'm finishing an ebook as we speak. This one's on Twitter though. I don't think I'll do another ground-up from-scratch technical book. I have amazing respect for guys like Ross Mistry who keep banging out books.

**Denis: What SQL books are on your bookshelf?**

SQL Server Query Performance Tuning Distilled by Grant Fritchey
  
SQL Server 2005 Performance Tuning by Steven Wort, Christian Bolton, Justin Langford, and more
  
Inside SQL Server 2008 T-SQL Querying by Itzik Ben Gan and others
  
Oracle and VMware by Bert Scalzo
  
The Wine Trials 2010 by Robin Goldstein and Alexis Herschkowitsch (databases and drinks go together like developers and cursors)
  
And tons more, but that's a good start.

**Denis: Who are your favorite authors?**

I really like reading James Rowland-Jones' chapter on Latching in our book. He has a really good sense of humor that plays well in print.

I like that Kevin Kline writes like he talks &#8211; he makes complex topics deceptively simple. Anybody can sandblast you with fifty-cent words, but Kevin distills things down in a way that's easy to read quickly.

Tim Ford spends entirely too much time writing his next book and needs to blog more, because he's hilarious, and I'm going through withdrawal.

My favorite authors of all time, though, would be Dave Eggers, Douglas Adams, and Garrison Keillor &#8211; and the poet W. H. Auden for good measure. I'm no Paul Randal &#8211; I think I read maybe 10 books in 2009, and I don't see that changing in 2010.

**Denis: How are you involved with SQLServerpedia?**

There's only two of us really heavily involved on a day-to-day basis. Brett Epps handles everything technical &#8211; if it involves PHP, graphics, MySQL, anything like that, that's his full time job. (I'm thankful for him, because when I was the guy doing that, the site looked bad and performed worse.) I handle everything else &#8211; working with the bloggers, checking article submissions, planning promotions, coming up with what we're going to do next. I get a lot of logistical support from a bunch of Questies &#8211; Christian Hasker, Andy Grant, Betsy Mendenhall, Heather Eichman, and Stephanie McCulloch &#8211; but most of the work is still me.

I love it. I really wish I had more hours in the day to spend on SQLServerPedia. We've got a million cool ideas, and it just comes down to manpower.

**Denis: Where can we expect to see you in 2010? Any conferences, seminars, trade shows or classrooms perhaps?**

EVERYWHERE, MUHAHAHA. Okay, maybe not. I'll be at the WorkTamer conferences in Canada, the Microsoft MVP Summit in Seattle, SQLSaturday Chicago, the PASS Summit in Seattle, and maybe the European one as well. I'm aiming for the Microsoft Certified Master program in March, and if that happens, I'll be off the radar for a few weeks.

I've got a day-long free virtual event coming in February with Kevin Kline and Ari Weil, too &#8211; we'll be talking DMVs all day long. More news soon…

That is it, thanks to Brent for the interview, you can visit Brent's site here: http://www.brentozar.com/ and you can follow him on twitter here: http://twitter.com/brento

To check out the book, visit the wrox site here: http://www.wrox.com/WileyCDA/WroxTitle/Professional-SQL-Server-2008-Internals-and-Troubleshooting.productCd-0470484284.html or Amazon here: [Professional SQL Server 2008 Internals and Troubleshooting][1]

 [1]: http://www.amazon.com/gp/product/0470484284?ie=UTF8&tag=sql08-20&linkCode=as2&camp=1789&creative=390957&creativeASIN=0470484284