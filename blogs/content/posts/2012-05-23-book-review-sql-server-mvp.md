---
title: 'Book Review: SQL Server MVP Deep Dives'
author: Jes Borland
type: post
date: 2012-05-23T13:08:00+00:00
excerpt: 'In another life, I’m a professional bookworm who gets to read and review books full-time. My latest SQL read was SQL Server MVP Deep Dives. It’s a compilation – 59 chapters – on various SQL Server topics, written by&hellip;'
url: /index.php/itprofessionals/professionaldevelopment/book-review-sql-server-mvp/
views:
  - 12438
rp4wp_auto_linked:
  - 1
categories:
  - Book Review
  - Professional Development

---
**  
** In another life, I’m a professional bookworm who gets to read and review books full-time.

 

<p style="text-align: center;">
  <a href="http://www.flickr.com/photos/kevharb/5466078025/lightbox/"><img src="http://farm6.staticflickr.com/5214/5466078025_a4d247aa50_n.jpg" alt="" /></a>
</p>

<p style="text-align: center;">
  <em>I&#8217;d like to live here, please.</em>
</p>

My latest SQL read was [SQL Server MVP Deep Dives][1]. It’s a compilation – 59 chapters – on various SQL Server topics, written by Microsoft MVPs. What&#8217;s so cool about this book, as opposed to most, is that it plays to an author&#8217;s strengths and passions. It doesn’t cover one topic; it covers every aspect of SQL Server. 

**Part 1 &#8211; Database Design and Architecture   
**    
I found this section relevant when I opened this book because I was starting two database design projects. Louis Davidson and Paul Nielsen’s database design ideas are useful for everyone to review from time to time.

**Part 2 &#8211; Database Development   
**    
The &#8220;Error Handling in SQL Server applications&#8221; chapter by Bill Graziano was immediately useful and impactful. Using “Try…Catch” and RAISERROR are often overlooked, but shouldn’t be, when writing T-SQL. 

“Introduction to XQuery” by Michael Coles also caught my attention, as learning it is one of my goals for 2012.

&#8220;Full-text searching&#8221; by Robert C. Cain is an awesome chapter. It covers how to set up, use, and maintain full-text catalogs and indexes. I&#8217;ve read about it before, but this had some of the best real-world examples. I had been looking at implementing it in a project at that time, so it was great timing.

Reading &#8220;Why every SQL developer needs a tools database&#8221; by LessThanDot’s very own Denis Gobo was cool. (You guys! I get to blog with authors and MVPs and really smart, amazing people!) Denis makes a great case for a separate database to hold shared functions. He also emphasizes the need for a numbers table, which I&#8217;ve recently re-acquainted myself with. Trust us, you want one.

I learned something brand new! (At 5:55 am, no less!) &#8220;Deprecation feature&#8221; by Cristian Lefter showed how to track the use of deprecated features through perfmon, a trace, or (my favorite) extended events. How did I NOT know about this? What a great audit tool. Now, how to effectively implement this&#8230;

I like the fact that non-SQL MVPs are also contributing. Even a (gasp) Access MVP.

**Part 3: Database Administration   
**    
“My favorite DMVs, and why” by Aaron Bertrand is one of my favorite chapters. DMVs are great. What I learned is that Aaron made his own sp_who! I wish I was still in a large corporate environment to test that. He also shows an uptime function that he created in his utility database, which he uses in almost all DMV queries. Good idea!

Ron Talmage wrote “Some practical issues in table partitioning”. It covered the basics &#8211; usually writing about this topic is overwhelming. I know I don’t fully understand partitioning yet. One thing I know: I think LEFT is easier to understand.

Scott Stauffer wrote “Successfully implementing Kerberos delegation”. In February, I had the pleasure of meeting Scott in Redmond, WA. I remember many Kerberos issues from my previous job. I never had a solid understanding of all the pieces until now. He provides a great great list of resources. I would love to see more people understand Active Directory and related services. (As Argenis Fernandez once tweeted, you can&#8217;t be really good at SQL Server until you get AD.) 

**Part 4 &#8211; Performance Tuning and Optimization   
**    
There are a few chapters on indexes here, because they are that important.

“How to optimize tempdb performance” by Brad McGehee includes many useful tips. Some are very basic, like splitting it into multiple files and having the files located on a different disk than data files. He also talks about pre-allocating space and how TDE affects tempdb.

Kevin Kline&#8217;s chapter, “Correlating SQL Profiler with Windows Performance Monitor”, blew me away. I always knew you could do it, but didn&#8217;t realize how easy it was. I wish I&#8217;d known this a year ago. 

“How to use Dynamic Management Views” by Glenn Berry is immediately useful, just like the ebook “[SQL Server DVM Starter Pack][2]” he wrote with Louis Davidson and Tim Ford. This chapter includes lots of queries you can run, work with, and edit further. DMVs continue to be one of the features I use most in SQL Server.

Cristian Lefter talks about Xevents in “XEVENT: the next event infrastructure for SQL Server”. I agree, these are the future of monitoring. It&#8217;s a good overview that summarizes key points. There are also a couple of easy-to-follow examples.

**Part 5: Business Intelligence** 

Erin Welker’s “BI for the relational guy” gives a good overview of how OLTP differs from OLAP. She discusses what Microsoft products are BI-related. There’s also a list of further reading material. (More books to put on my list&#8230;)

William Vaughn&#8217;s writing style for “U nlocking the secrets of SQL Server 2008 Reporting Services” had me giggling from start to finish. He gives a thorough overview of the many often -overlapping parts of SSRS. He also makes note of things like IIS no longer being required. 

“Expressions in SQL Server Integration Services” by Matthew Roche was an interesting read for me. I&#8217;m familiar with expressions in SSRS and thought this might be similar. Not really. Expressions are powerful, because they allow you to offer more than one option for some properties.

The book closes with an excellent chapter by Andy Leonard, “Incremental Loads using T-SQL and SSIS”. The first sentence had me giggling. The comparison between the two options is thorough and interesting.

This section made me realize how little I know about SSIS, SSAS, and data mining.

**59 Chapters Later…** 

I would recommend this book to anyone who works with SQL Server in any capacity. It touches on so many topics that anyone can find something of interest. Everyone is guaranteed to learn something. It’s easy enough to read one chapter in the morning, or at lunch, or before bed. Thank you to all of the authors who worked on it!

 [1]: http://manning.com/nielsen/
 [2]: http://www.red-gate.com/products/dba/sql-monitor/entrypage/dmv?utm_source=bradmcgehee&utm_medium=banner&utm_content=dmv_201007&utm_campaign=sqlresponse