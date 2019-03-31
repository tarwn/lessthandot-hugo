---
title: How I prepared myself for the MCSE certification
author: Koen Verbeeck
type: post
date: 2013-04-04T07:31:00+00:00
excerpt: 'This blog details my preparation for the MCSE - Business Intelligence certification.'
url: /index.php/datamgmt/dbprogramming/mssqlserver/how-i-prepared-mcse/
views:
  - 104620
enclosure:
  - |
    |
        http://mediadl.microsoft.com/mediadl/www/l/learning/video/certification/exam/repeated_answer_series.wmv
        613282
        video/x-ms-wmv
        
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - Microsoft SQL Server
tags:
  - business intelligence
  - certification
  - mcsa
  - mcse
  - microsoft
  - syndicated

---
<p style="text-align: justify">
  Last week I took the final exam to acquire the <a href="http://www.microsoft.com/learning/en/us/mcse-sql-business-intelligence.aspx#fbid=EmSJ9xHTLm0">MCSE – Business Intelligence</a> certification. This blog post describes my preparation for all of the exams. It is not a strict guideline of course, it’s just what suited best for me.
</p>

<p style="text-align: justify">
  The MCSE certification exists of 5 separate exams, of which the first 3 grant you the <a href="http://www.microsoft.com/learning/en/us/mcsa-sql-certification.aspx#fbid=EmSJ9xHTLm0">MCSA SQL Server</a> certification. These are the following:
</p>

<ul style="text-align: justify">
  <li>
    70-461: Querying Microsoft SQL Server 2012
  </li>
  <li>
    70-462: Administering Microsoft SQL Server 2012 Databases
  </li>
  <li>
    70-463: Implementing a Data Warehouse with Microsoft SQL Server 2012
  </li>
  <li>
    70-466: Implementing Data Models and Reports with Microsoft SQL Server 2012
  </li>
  <li>
    70-467: Designing Business Intelligence Solutions with Microsoft SQL Server 2012
  </li>
</ul>

<a style="text-align: justify" href="/media/users/koenverbeeck/MCSEPrep/MCSE.jpg?mtime=1365060226"><img style="float: left;margin-left: 10px;margin-right: 10px" src="/wp-content/uploads/users/koenverbeeck/MCSEPrep/MCSE.jpg?mtime=1365060226" alt="" width="300" height="171" /></a>

<div class="image_block" style="text-align: justify">
  The MCSA exams are the same for everyone; it doesn’t matter if you are going for the MCSE – Business Intelligence or for MCSE – Data Platform. Personally I like this decision, as everyone with the MCSA has the same common ground in the SQL Server platform. There’s always some overlap between the tasks a specific role is supposed to do. For example, DBA’s need SSIS from time to time to move data around and BI developers might need to set-up replication to feed a reporting database. Forcing everyone to do the same exams ensures certification holders have a certain skill set in the most important aspects of SQL Server.
</div>

<span style="text-align: justify">Before I started my certification spree on SQL Server 2012, I already had the three MCTS certifications for SQL Server 2008 (BI, DBA en database developer) and the MCITP: Business Intelligence Developer 2008. So I only had to refresh the older material &#8211; especially the DBA stuff – and learn all the new features. Why didn’t I took the upgrade exams? Because I like to challenge myself and I wanted to make sure I learned as much as possible in the progress.</span>

<p style="text-align: justify">
  <strong>70-463</strong>
</p>

<p style="text-align: justify">
  This is the first exam that I took. It deals mostly with SSIS, but has also some sections about DQS, MDS and dimensional modeling. I took this exam a year ago in beta, so there were no preparation materials available. Luckily I consider myself quite proficient in SSIS, so I didn’t need much preparation. After all, if you are going for one of the MCSE certifications, you should have at least one specialization in the SQL Server stack.
</p>

<p style="text-align: justify">
  I learned about the new SSIS features (which was Denali CTP1 at the time) by giving a session on the Belgian SQL Server Days in 2011:
</p>

<p style="text-align: justify">
  <a href="http://technet.microsoft.com/en-us/video/working-with-the-new-project-deployment-model-in-ssis-for-dummies-smarties.aspx"><em>Working with the New Project Deployment Model in SSIS for Dummies/Smarties</em></a><em> </em>
</p>

<p style="text-align: justify">
  You learn a lot about something by trying to teach others about it. Of course I also read a lot of blog posts about the subject, of which most of them are mentioned in the video. I gave the same presentation again for the SQLLunchUK user group, but this time through a LiveMeeting.
</p>

<p style="text-align: justify">
  A few years back I read <a href="http://amzn.to/1fpLRrp">The Data Warehouse Toolkit</a> by Ralph Kimball. Every serious BI professional should read this book, even if you are an adept Inmon follower. This book gives you all the details you need about dimensional modeling: star schemas, slowly changing dimensions, table grains et cetera. Microsoft relies heavily on the Kimball approach, in the data warehouse design but also in Analysis Services Multidimensional.
</p>

<p style="text-align: justify">
  Regarding Data Quality Services (DQS), I also mastered the basics by giving a session. This time a webinar for the Belgian 12 Hours of SQL:
</p>

<p style="text-align: justify">
  <a href="http://technet.microsoft.com/en-us/video/an-introduction-to-data-quality-services-dqs.aspx"><em>An introduction to Data Quality Services (DQS)</em></a>
</p>

<p style="text-align: justify">
  Most information about DQS I got from MSDN articles, such as these <a href="http://technet.microsoft.com/en-us/sqlserver/jj737674">How-To videos</a>, and from TechEd videos, like this excellent one from Elad Ziklik: <a href="http://channel9.msdn.com/Events/TechEd/NorthAmerica/2011/DBI207">Using Knowledge to Cleanse Data with Data Quality Services</a>.
</p>

<p style="text-align: justify">
  The last item on the list for this exam is Master Data Services (MDS). Since it only takes a small percentage of the questions and I’m not really convinced by the product – to be honest – I didn’t put much investigation to it. I read the free e-book <a href="http://amzn.to/1LlkyNj">Introducing SQL Server 2012 training kit</a>, so I at least knew how to handle the product.
</p>

<p style="text-align: justify">
  The exam itself went pretty well, although I struggled a bit with the <a href="http://mediadl.microsoft.com/mediadl/www/l/learning/video/certification/exam/repeated_answer_series.wmv">Repeated Answer Choices</a> questions, since it was the first time I ever saw those type of questions and took a few seconds before I realized what was going on J
</p>

<p style="text-align: justify">
  <strong>70-461</strong>
</p>

<p style="text-align: justify">
  Every person working closely with SQL Server, be it a DBA, a database developer or a BI developer, should know at least the basics of T-SQL. So this exam shouldn’t be too hard for most people. To prepare myself, I read the book <a href="http://amzn.to/1LlkInS">SQL Server 2012 High-Performance T-SQL Using Window Functions</a> by Itzik Ben-Gan to learn more about the windowing functions introduced in SQL Server 2012. I can absolutely recommend this book to everyone to get up to speed with Windowing Functions in SQL Server. Itzik really does a great job by explaining all the concepts very clearly and uses a lot of practical examples. A must-read.
</p>

<p style="text-align: justify">
  Next I read blog posts and MSDN articles about the new T-SQL functionality, such as TRY_PARSE, THROW and LAG/LEAD. <a href="http://msdn.microsoft.com/en-us/library/09f0096e-ab95-4be0-8c01-f98753255747">This MSDN page</a> gives a nice overview. Since the exam might contain questions that require you to write code, I refreshed my knowledge on the syntax of the several TSQL statements. Finally I re-read the chapters about T-SQL of the <a href="http://amzn.to/1PmAAUY">training kit for the 70-433 exam</a>, especially the one about XML. Because you know, I use this everyday so I’m an expert at it (uh-hum. Irony).
</p>

<p style="text-align: justify">
  I passed the exam on the first try, but I have to admit that the code writing questions are harder than it seems when you have no MSDN pages available to check your syntax.
</p>

<p style="text-align: justify">
  <strong>70-462</strong>
</p>

<p style="text-align: justify">
  This was the exam I feared the most. I’m certainly no DBA and although I already had the MCTS for administrating SQL Server 2008, I had to relearn almost everything again as I didn’t have much real-time practice regarding administration. So I took my time preparing myself for the exam, going over the <a href="http://amzn.to/1fpNe9s">training kit</a> a few times and watching some TechEd videos on Channel9.
</p>

<p style="text-align: justify">
  I did all the exercises of the training kit by setting up a virtual environment with a few servers using HyperV and doing all the DBA stuff such as mirroring, replication database, taking back-ups et cetera. The training kit briefly describes how to set this environment up, but it really doesn’t go in too much detail, so you’re pretty much on your own. If you have never heard of differencing disks for virtual machines before – like I did – prepare yourself to do some research. There was especially no information on how to set-up the networking, which appears to be crucial when you went to set-up clustering, as you need to have multiple networks. I briefly go over my set-up in this <a href="http://www.sqlservercentral.com/Forums/Topic1377933-10-1.aspx#bm1421996">forum thread</a>. The training kit and the matching practice tests also have some errors, so make sure to check out the <a href="http://oreilly.com/catalog/errataunconfirmed.csp?isbn=0790145345134">Errata</a> page of the book.
</p>

<p style="text-align: justify">
  The <a href="http://www.microsoft.com/learning/en/us/certification-exams.aspx#fbid=HsP2QW2phvi">new types of exam questions</a> (check the section <em>Exam formats and question types</em>) also include drag-and-drop questions, so I focused on learning the correct sequence to do something. For example, how to set up mirroring, replication or Availability Groups, how to do a partial restore, how to upgrade a cluster et cetera.
</p>

<p style="text-align: justify">
  All in all the training kit did a good job preparing me for the exam, as I passed it on the first try, but my score really wasn’t that impressive. But hey, what do you expect from a BI guy J Finishing this exam gave me the MCSA certification.
</p>

<p style="text-align: justify">
  <strong>70-466</strong>
</p>

<p style="text-align: justify">
  After a few months of well deserved “non-studying for exams” I decided to pick up the pace again and to start preparing for the 70-466 exam. I was in between projects at the time, so it was excellent timing to get some studying done.
</p>

<p style="text-align: justify">
  I started by reading the excellent book <a href="http://amzn.to/1Lll6Ts">Microsoft SQL Server 2012 Analysis Services: The BISM Tabular Model</a> by SSAS Maestros Chris Webb, Alberto Ferrari and Marco Russo, which comes at a whopping 656 pages. It’s a great book and it will teach you everything you need to know about tabular models and DAX, but actually it’s much too detailed for just exam preparation. But hey, a little too much knowledge doesn’t hurt anyone, right? So if you just want to get up to speed with SSAS tabular you can skip a few chapters, such as DAX Advanced for example.
</p>

<p style="text-align: justify">
  For SSAS multidimensional and Reporting Services, I just re-read the relevant chapters of <a href="http://amzn.to/1fpNzJi">the 70-448 training kit</a>, giving more attention to features I don’t use a lot, like pro-active caching, MDX (I know, sorry Chris Webb) and administrating SSAS and SSRS servers. I skipped the data mining chapter, as it apparently didn’t make the exam <em>Skills Measured</em> section. I digged deeper into SSAS and SSRS administration by reading relevant blogs and MSDN articles, such as <a href="http://msdn.microsoft.com/en-us/library/ms157403.aspx">Reporting Services Execution and Trace Logging</a>.
</p>

<p style="text-align: justify">
  The exam went pretty well on the first try and thus I had only one exam left for the MCSE certification!
</p>

<p style="text-align: justify">
  <strong>70-467</strong>
</p>

<p style="text-align: justify">
  The exam was really interesting to study for. You can compare it a bit to the MCITP – Business Intelligence exam. What makes it so interesting is that everything comes together in this exam. You need to know about SSIS, SSAS (multidimensional and tabular), SSRS (Report Designer, Report Builder, Power View, PowerPivot and PerformancePoint) and SharePoint integration in order to succeed. Everything just comes together.
</p>

<p style="text-align: justify">
  To prepare myself I went over <a href="http://www.microsoft.com/learning/en/us/course.aspx?id=20467a#fbid=HsP2QW2phvi">course 20467A: Designing Business Intelligence Solutions with Microsoft SQL Server 2012</a>, which is a fascinating read. I revisited some chapters about administration for SSRS and SSAS and I dug a bit deeper into SharePoint integration (but let’s face it, I’ll never get that Kerberos authentication thingy).
</p>

<p style="text-align: justify">
  The exam itself wasn’t that easy, but I managed to get through, so now I’m the proud holder of the MCSE – Business Intelligence certification!
</p>

<p style="text-align: justify">
  <strong>Conclusion</strong>
</p>

<p style="text-align: justify">
  This was my entire preparation. I’m done now with exams for a while J
</p>

<p style="text-align: justify">
  If you have interesting reading materials or videos that can help other preparing themselves for their exams, please share them in the comments!
</p>