---
title: How I got started with SQL Server
author: SQLDenis
type: post
date: 2009-09-07T17:45:04+00:00
ID: 551
url: /index.php/datamgmt/dbprogramming/how-i-got-started-with-sql-server/
views:
  - 5352
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
tags:
  - meme
  - sql server

---
I got tagged by Denny Cherry ([blog][1] | [twitter][2]) who himself got tagged by Brent Ozar ([blog][3] | [twitter][4]), they want to know how I got started with SQL Server.

So here is my story. My first database was not SQL Server but DB2 running on a mainframe; this was about 10 years ago and back then you did not have fancy keyword highlighting because everything was green and in CAPS...oh the memories of SPUFI, MVS, COBOL, JCL and CICS...good riddance!

**Silicon Alley Job**
  
I January 2000 I started to work in Silicon Alley (21st and Broadway). I started as an <s>ancient</s> classic ASP developer hitting an Access database (this got converted to SQL Server 6.5 or 7), do any of you remember [Chilisoft ASP][5]? Then I switched from ASP to ColdFusion which was basically very similar to VB but you put everything in tags and prefixed it with CF...so for example 

```
<cfif expression>
   HTML and CFML tags
<cfelseif expression>
   HTML and CFML tags 
<cfelse>
   HTML and CFML tags
</cfif>
```

When I developed in ColdFusion we were using SQL Server 7 and we actually used stored procs and parameters instead of cfquery with inline SQL. After that we used JSP and also ATG Dynamo. The company I worked for was a GREAT design company, they won a ton of awards. We had this art director who made sure that everything looked the same on Netscape 4.7, IE 5, IE 5.5 for the Mac etc etc. I would sometime spend 3 hours to make sure that something looked the same on all browser only to be told that it is off by 1 pixel on IE 5.5. This person was one of the reasons I liked DB work more and more and front end programming less and less (probably helped by the fact that I don't have any design skills). For the last project we used SQL Server 2000 and then after September 11 2001 we did not get any new work. We all got laid off in October and it took me about 4 weeks to find a new job.

**Midtown Job**
  
This new job was for SQL only, no front end work at all. The job was for 2 months, it got extended to 4, then 6 and then they made me full time and I stayed with them for 3.6 years. For the first 3 months all I was doing was creating DTS packages. This job was great for me, it was walking distance from the Upper East Side where I lived (it was about 50 blocks but I consider that walking distance :-)) The people were great and we had flexible hours; sometimes I would start at 7 sometimes at 9. At this job I had to interact with NJ Transit, LIRR, MNRR, New York City Transit, State of New Jersey and City of New York. The interesting thing is that they give you a file in a format that they choose and you also have to create a file in their format. At this job is also were we got saved by a SQL cluster, we put a cluster in place and a week after that the motherboard got fried...that was good timing. I did the usual SQL stuff here like DTS, creating procs, creating data models etc. etc. etc. Of course we didn't have a SQL admin so we were the accidental DBAs. I worked with 4 other SQL developers on a big project but usually it was only 2 SQL developers. I moved to Princeton New Jersey after being at this job for about 2 years. After over 3 years at this job our big client was acting suspicious and doing some things that were indicators that they were going to drop us. I started to look for a new job, I got an interview with a company 4 miles from my home and during the 5 hours interview we all got laid off. Luckily for me the company made me an offer the same day and my last day was June 30 and the old place and July 1st at the new place.

**Princeton Job**
  
I have been a little over 4 years now at my current job, I am still a SQL developer and almost don't do any DBA kind of things. I am still doing a lot of SSIS, importing and exporting data, doing some SSIS (but no SSRS). I also have to work with different databases like Sybase IQ, Access and FoxPro, then you have a bunch of different text files and Excel files that you need to import and export. We already have SQL 2008 boxes and will be only on 2008 on 7 boxes sometimes in the fall. The biggest challenge at this job is how to make a lot of data perform well. I have employed 'tricks' like partitioned views (on SQL 2000) and this made queries much faster. Currently I am testing with compression, vertical partitioning and partition function and I am seeing queries go down from a second to 17 millisecond, going to 2008 looks good.

I will also tag some people, the list is below
  
[onpnt][6]
  
[sqlfool][7]
  
[Mike Walsh][8]



\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][9] forum or our [Microsoft SQL Server Admin][10] forum**<ins></ins>

 [1]: http://itknowledgeexchange.techtarget.com/sql-server/how-i-got-started-in-it/
 [2]: http://twitter.com/mrdenny
 [3]: http://www.brentozar.com/archive/2009/04/starting-the-sql-journey/
 [4]: http://twitter.com/brento
 [5]: http://www.sun.com/software/chilisoft/
 [6]: /index.php/All/?disp=authdir&author=68
 [7]: http://sqlfool.com/
 [8]: http://www.straightpathsql.com/blog
 [9]: http://forum.ltd.local/viewforum.php?f=17
 [10]: http://forum.ltd.local/viewforum.php?f=22