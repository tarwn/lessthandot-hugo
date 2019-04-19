---
title: LessThanDot Bloggers Weekly 12/11
author: Ted Krueger (onpnt)
type: post
date: 2011-12-16T16:39:00+00:00
ID: 1446
excerpt: |
  This week was an active week for the bloggers on LessThanDot.  Here is a recap on everyone's contributions.
  Week 12/11 – 12/15
  Saturday started with Denis Gobo continuing an excellent series titled, “SQL Advent 2011”.  This week's daily blogs for Deni&hellip;
url: /index.php/itprofessionals/professionaldevelopment/lessthandot-bloggers-weekly/
views:
  - 3073
rp4wp_auto_linked:
  - 1
categories:
  - Professional Development

---
This week was an active week for the bloggers on LessThanDot.  Here is a recap on everyone's contributions.

**Week 12/11 – 12/15**

Saturday started with [Denis Gobo][1] continuing an excellent series titled, “SQL Advent 2011”.  This week's daily blogs for Denis's series were

[SQL Advent 2011 Day 15: Joins][2]

FULL OUTER JOIN scares me a little when it comes to really big tables and SSIS 😉  There are a lot of blogs out there describing JOINs but this one and the examples provided are a great, brief and to the point on how each JOIN is used and the resulting output.

[SQL Advent 2011 Day 14: EXCEPT and INTERSECT SET Operations][3]

EXCERT and INTERSECT are defined and examples shown in this post.  The examples do a great job with showing these set operations at work.

 

[SQL Advent 2011 Day 13: DDL Triggers][4]

DDL Triggers you say?  I can have a trigger on my database for catching events like a CREATE TABLE?  I can stop those pesky developers from doing the nasty?  Yes!  Denis goes over the foundation in this blog.  Now, be warned.  How many servers have gone down or databases become unusable due to the advent of the DDL trigger?  Be careful here everyone!

[SQL Advent 2011 Day 12: Table Value Constructor][5]

Ah, the TVC.  How I learned to love thee.  Really, out of everything became useful immediately and was a welcome enhancement.  Remember those old Classic ASP days where you'd build a really long and nasty string with hundreds of “INSERT INTO....”  Well, thankfully, this was a great addition to SQL Server.

[SQL Advent 2011 Day 11: DML statements with the OUTPUT clause][6]

Denis goes over the OUPUT Clause and utilizing it to return the effected rows from using UPDATE/INSERT/DELETE (DML) transactions.  This is very cool and plenty of usage in processing ETL jobs and validations.

[][1]

### [Jes Borland][1]

[T-SQL Tuesday #25: T-SQL Tricks – Checking Transaction Log Space Used][7]

Jes Borland added to the week starting off with the monthly T-SQL Tuesday post.  This month she went over a trick she uses to gather transaction log usage for SQL Server.  Jes does a good job of talking about the script she wrote and the use of SQLPERF as well as the DMV clear options it has.  I really like the added description for security needed around the script she posted.

[#meme15: I'm A Blogger. Why?][8]

This was a new monthly posting that Jason Strate started up and Jes joined in.  The topic this month was, why you blog and continue to blog.  I'm happy to read Jes's post on this.  Although she hints that it was me that helped her get into blogging, we're really the lucky ones on LessThanDot to have her here.

Jes got a last minute, [Friday post in for us][9].  This post is on a professional development side of the fence.  Jes recently went into consulting for the first time in her career and these are some high points of what she has learned so far.  Yes, consulting isn't like your everyday fulltime job.

  * Ask a Million Questions. Then Ask One More. 
  * Depend on Your Network 
  * Be Flexible 
  * Know Your Tools 
  * A database is a database is a database. A language is a language is a language. 

As a consultant I can agree with all of these and quickly adapting is why I think Jes will be successful in this new career path.

[][10]

#### [Ted Krueger][10]

I contributed this week with topics on Replication, Orphaned Database Users, SSIS and my own write-up of why I blog and continue to do so.  Here is a list of those blogs.  I hope one of them helps someone out.  I know some of these lessoned learned from being in the field so long have proven valuable to me and hope to pass that to others.

[Why I started and continue to blog – #Meme15 on Blogging][11]

[SQL Server Integration Services tools to have – Configuration File Editor][12]

[Troubleshooting Merge Replication Performance – Indexing Publications][13]

[Fixing orphaned database users in 2005 to 2012 – T-SQL Tuesday #025][14]

[Troubleshooting Merge Replication – Logging][15]

 

#### [George Mastros][16]

blogged this week about **[Zero-One-Some Testing][17]**. George goes over this method of testing extremely well.  One line I'd like to point out that I think makes the most impact and value from George's blog is, ****

_“Tests for zero records seem to uncover missing requirements or defects in code involving aggregations or in places where programmers assume that there will simply be data (perhaps because their test database already has data in it).”_ __

Need I say more?  I recommend reading this blog highly and following the direction.   ****

[][18]

#### [Eli Weinstock-Herman][18]

blogged this week about using the Continuous Delivery Model for his home lab.   Eli shows us the setup that is planned and a “next steps” preview.  I'm looking forward to this mostly because if Eli is doing it, it means I will probably want to pay attention and follow suite in my own labs at home.

One part that sticks out on the concept and reasoning for why you would want to stick to this model

_“When I spend a weekend playing with caching in my ASP.Net project, I want to be able to walk away from the project knowing it still works and I won't be spending my next Saturday trying to figure what I did to break my test systems. This project will also provide a future testbed for load testing and static analysis tools”_

How many times was that you? I know it was me more than a few times.

Eli follows that blog up with [Continuous Delivery Project – Making MVCMusicStore Testable][19] to really kick it off and stay to his word.

**Milestones on LessThanDot** 

Denis Gobo and Christiaan Baes went over 400 blog posts a while ago.  400!  That is remarkable and a great accomplishment.  I feel like I've been writing forever myself and I'm nowhere near that level.

Jes Borland is over the 50 posts hump.  It's all uphill from here!

As always, thank you to the LessThanDot bloggers for your contributions.  The site has evolved into a highly respected resource on the web and it's great to see it building.

 [1]: /index.php/All/?disp=authdir&author=420
 [2]: /index.php/All/?p=1545 "SQL Advent 2011 Day 15: Joins"
 [3]: /index.php/All/?p=1542 "SQL Advent 2011 Day 14: EXCEPT and INTERSECT SET Operations"
 [4]: /index.php/All/?p=1539 "SQL Advent 2011 Day 13: DDL Triggers"
 [5]: /index.php/All/?p=1536 "SQL Advent 2011 Day 12: Table Value Constructor"
 [6]: /index.php/All/?p=1533 "SQL Advent 2011 Day 11: DML statements with the OUTPUT clause"
 [7]: /index.php/All/?p=1538 "T-SQL Tuesday #25: T-SQL Tricks - Checking Transaction Log Space Used"
 [8]: /index.php/All/?p=1544 "#meme15: I'm A Blogger. Why?"
 [9]: /index.php/ITProfessionals/ProfessionalDevelopment/on-being-a-consultant-lessons-1
 [10]: /index.php/All/?disp=authdir&author=68
 [11]: /index.php/All/?p=1543 "Why I started and continue to blog - #Meme15 on Blogging"
 [12]: /index.php/All/?p=1534 "SQL Server Integration Services tools to have – Configuration File Editor"
 [13]: /index.php/All/?p=1530 "Troubleshooting Merge Replication Performance – Indexing Publications"
 [14]: /index.php/All/?p=1537 "Fixing orphaned database users in 2005 to 2012 - T-SQL Tuesday #025"
 [15]: /index.php/All/?p=1529 "Troubleshooting Merge Replication – Logging"
 [16]: /index.php/All/?disp=authdir&author=10
 [17]: /index.php/DataMgmt/DBProgramming/zero-one-some-testing
 [18]: /index.php/All/?disp=authdir&author=9
 [19]: /index.php/All/?p=1510 "Continuous Delivery Project - Making MVCMusicStore Testable"