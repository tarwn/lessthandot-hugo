---
title: Interview With Rod Colledge About The Book SQL Server 2008 Administration in Action
author: SQLDenis
type: post
date: 2009-10-12T16:35:02+00:00
url: /index.php/datamgmt/datadesign/interview-with-rod-colledge-about-the-bo/
views:
  - 49750
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - administration
  - book
  - interview
  - sql server 2008

---
I read [SQL Server 2008 Administration in Action][1] and really like this book, I have reviewed SQL Server 2008 Administration in Action here: [Review of SQL Server 2008 Administration in Action][2]

I decided to ping Rod Colledge about doing an interview and he said yes. Some of the questions came from our forum users in the following thread: http://forum.ltd.local/viewtopic.php?f=17&t=8089

Below is the interview.

**What level of competence should a reader have before reading this book? Is this book good for accidental DBAs?**

It’s interesting that you ask about the &#8220;accidental DBA&#8221;. I recently blogged [The Great DBA Schism][3] in which I noted that these types of DBAs are becoming much more common. My book is perfect for these DBAs as they’re typically bright people with experience in other areas who want to quickly learn DBA best practices without having to wade through lots of text. 

A lot of books patronize their readers and drag them through painful reproductions of simple tasks. Worse still, these books often reproduce information that is easily accessible from other free sources, for example, Books Online. As a result, the page count blows out, and most of the book goes unread.

With my book, I’ve avoided reproducing such information and spent more time on explaining why things are important, and where appropriate, directed the reader to the appropriate resource for more information should they need it. The end result is a concise book, and one that concentrates on best practices. 

I’ve had great feedback from both experienced and new DBAs, and although the book has “SQL Server 2008” in its title, its major themes are applicable to earlier versions as well.

**What are some of the pitfalls that catch DBAs over and over again?**

SQL Server is often sold on the premise of a low administration overhead. While that’s true (and good), it often results in DBAs becoming complacent about the database and ignoring important proactive maintenance tasks. Things like relying on autogrow and leaving the database in full recovery mode (without taking transaction log backups), are classic examples of this complacency.

**What are the most important things a person can do to become an exceptional DBA?**

Become familiar with your customer’s service level agreements (SLAs), be cautiously pessimistic when planning for disaster, and spend time each day on research/learning.

Firstly, SLAs. SLAs are crucial in order to build appropriate solutions that meet the customer’s expectations without wasting money better spent in other areas. DBAs often design database infrastructure in the absence of SLAs. That’s like an architect who assumes their client doesn’t need any more than 3 bedrooms, but must have a full size tennis court in the backyard.

Secondly, disaster recovery planning. Disasters happen, and we need to be prepared for a wide variety of them. The last thing you want is to be confronted with a production problem at 3am that you’ve never thought about before. In those situations, the adrenaline is surging and you’re likely to make silly mistakes. Simulate a wide variety of different types of disasters, and practice recovering from them in a test environment. When they happen for real, you’ll be more prepared and calm. Further, this process exposes any weaknesses you have in your current setup, and gives you the chance to fix them before an actual disaster strikes.

Finally, research. There’s only so much you can learn in your current job, and the longer you stay in that job, the less opportunity you have to further your knowledge. Regularly reading SQL Server newsgroups/forums are a fantastic (and low risk) way of expanding your horizons by enabling you to peer into other people’s problems, think about how you would go about solving them, and seeing how others would solve them.

**Can you list any third party tools that you find useful to have as a SQL Server developer/admin?**

I can’t live without Redgate’s [SQL Compare][4] and Quest’s [Litespeed][5]. I use both of those on a daily basis.

**Do you think the resource governor in sql server 2008 answers the DBAs prayers to finally keeping ad-hoc queries from killing overall database server performance?**

Almost 🙂 It’s a great move in the right direction. If only we could constrain disk and network resources in the same way as CPU and memory. Hopefully that will be addressed in the next version of SQL Server.

The other interesting thing about Resource Governor is that as well as constraining resource usage, we can also use it to measure resource usage by group, and use that for time costing purposes &#8211; like they used to do in the old mainframe days.

**Will DBAs finally realize high availability with mirroring is not a disasterrecovery solution after reading SQL Server 2008 Administration in Action?**

Great question. High availability and disaster recovery are different things, and DBAs often confuse the two. High availability solutions such as database mirroring are designed to increase the availability of a database during planned and unplanned downtime. Disaster/recovery planning is about all of the things that need to happen before, during and after a disaster to minimize data loss.

Effective disaster recovery planning is as much a state of mind as it is a feature or switch. DBAs often make the mistake of thinking that their disaster recovery planning begins and ends with a single feature such as database mirroring. Mirroring will not automatically protect every database on a server, nor will it enable you to recover to a specific point in time, and unless the client connections are SNAC enabled, they won’t automatically redirect to the mirroring partner in the event of a failure.

Mirroring is a great feature, but it has its limitations, and needs to be used in the appropriate way.

**For seasoned DBAs that have seen most of the bad things that go along with the job, would SQL Server 2008 Administration in Action still benefit them and are there particular sections you recommend to key in on for those level DBAs?**

At the close of each chapter, I’ve got a summarized list of best practice bullet points. Perhaps the best way for experienced DBAs to read the book is to read the best practice list first, and then open the chapter to the appropriate section should they need further information/clarification.

For experienced DBAs that haven’t yet upgraded to SQL Server 2008, I’ve made sure that new 2008 features are clearly marked, so they can target-read those sections. I’ve also spent a bit of time on the 2008 upgrade process in the installation chapter, including different techniques to minimize the upgrade time, such as using transaction log shipping.

**What&#8217;s the next step for a DBA after reading this book?**

Each chapter has a link to a specific page on [SQLCrunch.com][6], the website I created and run. This site includes summarized links to best practice articles, whitepapers and blogs from Microsoft and other SQL Server MVPs. This is a great way to obtain further information on topics of interest.

My hope is that DBAs will also read Appendix A, Top 25 DBA worst practices, and try and eliminate as many as those as possible.

**Does a DBA need to know T-SQL to do his job effectively?**

Yes. Manually clicking through wizards is possibly ok for managing one or two small servers, but automation is crucial for managing large and complex environments. Anything that occurs more than once should be scripted and scheduled with appropriate monitoring and alerting systems.

One of the nice things about SQL Server 2008 (also included in 2005) is that most dialog boxes include a “script” button that converts what you’ve just done in the dialog box into a T-SQL script. This is a great way of learning how to use T-SQL, as well as understanding what SQL Server is doing behind the scenes.

**What new features in Sql Server 2008 are you most excited about as a DBA?**

Policy-based management is without a doubt the best new feature for DBAs. It enables us to start moving from exception-based management (dealing with all the things that go wrong) to intent-based management; for example, make this production server the same as all those other production servers. For large and complex SQL Server environments, it’s absolutely brilliant, particularly when combined with the Central Management Servers feature which lets us evaluate, and in some cases, correct, policy compliance across large groups of servers in a single action.

The Management Data Warehouse and Data Collector features are also very welcome, enabling us to easily collect performance related metrics for use in a baseline analysis exercise. Resource Governor, which we spoke about earlier, is also great, particularly for constraining resources in mixed use environments such as servers used for both line of business data entry and reporting.

The list goes on; backup compression, transparent data encryption and automatic page recovery with database mirroring. These are all great features, and makes our lives a lot easier.

**Which chapter was the hardest to write and can you explain why?**

The first chapter (which doesn’t exist anymore) 🙂 I wrote that chapter about 9 times in various different ways as I searched around for the best “feel” for the book.

Writing a (good) book is hard work. You need to engage the reader, gain their trust, and hopefully teach them something as well. I hope I’ve succeeded in that task.

**What SQL books are on your bookshelf?**

Grant Fritchey’s Query Performance Tuning Distilled, and Kalen Delaney’s Inside/Internals series. I’m also spending more time in the Business Intelligence space, so I’m stocking up on a lot of books in that area as well. 

I was fortunate to be involved in the production of the upcoming SQL Server MVP Deep Dives book which features collected works from your good self :-). That’s going to be a blockbuster book for SQL Server fans, and I can’t wait to get the hard copy at SQLPass next month.

**Where can we expect to see you in 2009/2010? Any conferences, seminars, trade shows or classrooms perhaps?**

You can see me on the golf course and at the bar 🙂 Beyond that, I’m looking forward to [speaking at SQLPASS][7] this year, where I hope to meet lots of fantastic and like minded SQL Server enthusiasts. I also enjoy travelling and presenting at local SQL Server user groups, so I’m hoping to be invited to speak at lots of different groups next year!

Thanks again to Rod for doing the interview, below are 2 chapters that you can download to check out the book

Sample chapter 4 [Installing and upgrading SQL Server 2008][8]
  
Sample chapter 10 [Backup and recovery][9]

There is some more info about the book on the publisher&#8217;s website: http://www.manning.com/colledge/

You can also checkout the book on Amazon for more reviews: [SQL Server 2008 Administration in Action][1]



\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][10] forum or our [Microsoft SQL Server Admin][11] forum**<ins></ins>

 [1]: http://www.amazon.com/gp/product/193398872X?ie=UTF8&tag=sql08-20&linkCode=as2&camp=1789&creative=390957&creativeASIN=193398872X
 [2]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/review-of-sql-server-2008-administration
 [3]: http://www.rodcolledge.com/rod_colledge/2009/08/the-great-dba-schism.html
 [4]: http://www.red-gate.com/products/SQL_Compare/index.htm
 [5]: http://www.quest.com/litespeed-for-sql-server/
 [6]: http://sqlcrunch.com/
 [7]: http://summit2009.sqlpass.org/Agenda/ProgramSessions/DBAsBehavingBadlyWorstPracticesfor.aspx
 [8]: http://www.manning.com/colledge/SampleCh04.pdf
 [9]: http://www.manning.com/colledge/SampleCh10.pdf
 [10]: http://forum.ltd.local/viewforum.php?f=17
 [11]: http://forum.ltd.local/viewforum.php?f=22