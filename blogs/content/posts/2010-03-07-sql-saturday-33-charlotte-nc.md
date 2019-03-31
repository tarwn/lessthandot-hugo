---
title: SQL Saturday 33 – Charlotte, NC
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2010-03-07T20:01:53+00:00
url: /index.php/datamgmt/dbprogramming/sql-saturday-33-charlotte-nc/
views:
  - 4696
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - dba
  - sqlsaturday
  - sqlserver

---
This weekend was SQL Saturday #33 in Charlotte NC and also the first one I attended. It&#8217;s been a long weekend, but I wanted to get my thoughts down while they&#8217;re still fresh. Be warned, it&#8217;s been a long weekend, so my grammar and attempts to reign in my propensity for verbosity have both gone out the window.

## Background

Sorry SQL Saturday? Tarwn and SQL? We have questions and we know how painful it is for you to write short blog posts&#8230;

### SQL What?

[SQL Saturday][1] is the name for a series of 40+ SQL events that occur throughout the US. Each event is managed locally by SQL user groups and PASS chapters and sticks to a few guidelines set down by Andy Warren (<a href="http://www.sqlservercentral.com/blogs/andy_warren/default.aspx" title="Andy Warren's blog>blog</a> | [twitter][2]), Steve Jones ([blog][3] | [Twitter][4]), and Brian Knight ([blog][5] | [twitter][6]), the creators of SQL Saturday. This year the reins for SQL Saturday are being passed to [PASS][7] in order to help manage the logistics of the growth of the events. There is a lot more information available on the [SQLSaturday website][8], including upcoming dates and locations for more events.

### My Involvement

So obviously I am not a SQL guy and you may be wondering what I was doing at a SQL Saturday. As you know, LessThanDot has some really good SQL people that frequently write articles, offer support in multiple forums, and support their respective communities. This year we have decided to begin supporting the community in a more active role by supporting SQL Saturday #31 in Chicago. As we continue to grow we hope we will be able to extend our support in other ways, but that&#8217;s less relevant at the moment. I&#8217;ll be present at SQL Saturday #31 to help show support &#8220;(and hopefully sneak into a few sessions as well).

When the local call went out for attendees to SQL Saturday in Charlotte, I jumped on the opportunity to not only meet some of the local(-ish) community, but also to take advantage of the learning opportunities from the sessions and from the event itself. While I was able to dive deeper into a number of topics that interest me I was also able to ask some questions about the logistics of the event and planning. 

## The Sessions

I made it to the following sessions:

**<u>[Insight Into Your Indexes with DMVs][9]</u>
  
Timothy Ford** ([blog][10] | [twitter][11])
  
An excellent session using DMVs to manage, maintain, and locate unused indexes or potential targets for tuning. This was a good session because I had already used everything he pointed out, but he gave me more depth (as well as a number of tricks) that I didn&#8217;t previously have.

**<u>Is Virtualization a Good Choice for SQL Server?</u>
  
Denny Cherry** ([blog][12] | [twitter][13])
  
Having previous virtualized servers (as well as managed the random set of SQL servers), I was eager to start filling a gap in my education, namely putting the two together. Denny did an excellent introduction into virtualization of SQL Server, critical points that need to be determined when considering virtualization, and a live view into his own, very large environments. 

**<u>Powershell Awesomesauce</u>
  
Aaron Nelson** ([blog][14] | [twitter][15])
  
I went into Aaron&#8217;s session with almost no knowledge of powershell and came out with some ideas and practical examples of where it could be used to manage environments and automate work. While some of the concepts clicked quickly for me (&#8216;piping&#8217; output is common in Unix-like systems), others were new to me and his examples hit a good balance between brevity in code length and breadth of functionality. He also pointed out a number of good follow-up resources and reading recommendations.

**<u>Maximizing Plan Re-Use</u>
  
Andrew Kelly** ([blog][16] | [twitter][17])
  
The concept of executions plans is still fairly new to me, something I had little knowledge on prior to Andrew&#8217;s session. Andrew presented a good mix of knowledge about plans, recompilation of plans, reasons, etc. and active examples to help us tie the concepts to the code. While I&#8217;m by no means an expert after one session, he filled in some critical gaps in my own learning and helped connect some dots from past tuning experiences.

**<u>Storage for the DBA</u>
  
Denny Cherry** ([blog][12] | [twitter][13])
  
As Denny put it in the beginning, often there is a disconnect between DBA&#8217;s and Storage Admins that comes down to some fundamental differences in terminology. This session was a &#8216;How to Speak Storage Admin&#8217; class that walked us trough the basics of terminology, tools and methods to help size your environment, and some advanced tools he uses in his own environment. And excellent session, even if I am a developer and not a DBA 🙂

**<u>[Top 10 Mistakes on SQL Server][18]</u>
  
Kevin Kline** ([blog][19] | [twitter][20])
  
An excellent session from Kevin on the top 10 <u>DBA</u> mistakes he has seen in his experiences. From lack of troubleshooting methodology to not testing backups, he covered a number of assumptions and bad practices that we have probably all been guilty of, at one time or another.
  
_Note: The linked slides page above has the 2005 version of this Top 10&#8230; slides, but not the newer 2008 one he presented yesterday. I assume that will be posted soon, though_

## The Community

The community was probably the best part of the event. Beyond getting to learn from so many people (for free!) it was great to actually meet them face to face. Someone repeated the &#8220;Our rockstars are not the same as your rockstars&#8221; comment at one point during the day, and I couldn&#8217;t agree more. It was just downright cool to meet people that previously I had only talked with on twitter and forums, or seen as names on book covers and faces in webinars. The down-to-earth, we&#8217;re-all-part-of-the-same-community feeling was a great change from the larger conferences that (not that I don&#8217;t appreciate those as well). Instead of being a conference event, the day had a more fun, get-together feel to it.

And the fact that Greg ([SQLSensei][21]) picked up my bar tab and I got a raffle drawing prize from Kevin (<a href="http://twitter.com/kekline" Title="kevin on Twitter (again)">kekline</a>) definitely didn&#8217;t hurt. Peter ([Peter_Shire][22]), Greg ([SQLSensei][21]), and the rest of the group at Charlotte put together an excellent event.

## Final Word

The event was a resounding success. This was my first SQL Saturday and already I&#8217;m looking forward to my next one, and the one after that. The SQL community is amazing and I think that these events really drive it home. I urge everyone to support your local SQL Server user groups and events like SQL Saturday, as this will only help the community continue growing and continue to be a close-knit and fun community to be a part of. 

I have also, after urging from a number of people, started giving thought to offering to speak at few events myself. While I&#8217;m not nearly the SQL guru that the other speakers are, I&#8217;ve been in the trenches for a while, broken my fair share of servers, and could probably find one or two things to pass on.

 [1]: http://sqlsaturday.com/about.aspx "More About SQL Saturday (Visit the Site)"
 [2]: http://twitter.com/SQLAndy "Andy Warren on Twitter"
 [3]: http://www.sqlservercentral.com/blogs/steve_jones/default.aspx "Steve Jones's blog"
 [4]: www.twitter.com/way0utwest "Steve Jones on Twitter"
 [5]: http://www.bidn.com/blogs/BrianKnight "Brian Knight's Blog"
 [6]: http://twitter.com/brianknight "Brian Knight on Twitter"
 [7]: http://www.sqlpass.org/ "Visit the  Professional Association for SQL Server website"
 [8]: http://sqlsaturday.com/ "Hit the website"
 [9]: http://thesqlagentman.com/presentationfiles/ "Click here for Presentation Files"
 [10]: http://thesqlagentman.com/ "Timothy Ford's blog"
 [11]: http://twitter.com/SQLAgentman "Timothy Ford on Twitter"
 [12]: http://itknowledgeexchange.techtarget.com/sql-server/ "Denny's blog"
 [13]: http://twitter.com/mrdenny "Denny Cherry on Twitter"
 [14]: http://sqlvariant.com/wordpress/ "Aaron Nelson's blog"
 [15]: http://twitter.com/SQLVariant "Aaron Nelson on Twitter"
 [16]: http://sqlblog.com/blogs/andrew_kelly/ "Andrew Kelly's blog"
 [17]: http://twitter.com/gunneyk "Andrew Kelly on twitter"
 [18]: http://kevinekline.com/slides/ "Kevin's Slides"
 [19]: http://kevinekline.com/ "Kevin Kline's blog"
 [20]: http://twitter.com/kekline "Kevin Kline on Twitter"
 [21]: http://twitter.com/sqlsensei "Greg on Twitter"
 [22]: http://twitter.com/peter_shire "Peter on twitter"