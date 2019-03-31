---
title: Toughest SQL Challenges
author: Alex Ullrich
type: post
date: 2008-12-17T14:39:59+00:00
url: /index.php/datamgmt/datadesign/toughest-sql-challenges/
views:
  - 7976
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
[Chris Shaw posted a new SQL Quiz][1] where he asks: &#8220;What are the largest challenges that you have faced in your career and how did you overcome those?&#8221;

[Denny Cherry (@mrdenny on twitter)][2] tagged [SQLDenis][3] who tagged [George][4] who tagged [Ted][5] who then tagged [Chrissie (the Great)][6] who then tagged me.

For some reason, SQL has always seemed very natural to me, so I tend to run into more problems with front-end languages (and writing blog posts in a timely fashion, apparently!). But of course, no matter how naturally things come, there will always be challenges (else what fun would it be?).

I guess the biggest challenge I had was a week or two after I took my first SQL Server job. I had no experience with SQL Server, and my only real database experience was writing simple queries against some Sybase db&#8217;s to streamline QA processes at the previous company. At this job, one of my bigger responsibilities was to maintain these two big reporting databases. These db&#8217;s were populated from our own daily processes, as well as files received monthly from different vendors and clients, and both were littered with problems. 

So I&#8217;d been there for a week, and we got a call from client x (a large health insurance provider) saying that the numbers we&#8217;d reported for campaign z were way off (something like 3x the number of applications they had on the books from this campaign). So my introduction to this clients&#8217; database began. What I saw was nothing short of appalling. The monthly loads were being done by DTS packages that had not even a hint of data cleansing or error handling. I actually found binders full of looseleaf pages inscribed with individual lead id&#8217;s that were deleted (manually) from the file each month. Yikes.

But it got worse. The legend responsible for keeping these tomes had eventually decided &#8220;if I disable the constraints on these tables, then I won&#8217;t have to do all this manual data scrubbing each month, and that sure would be more efficient&#8221; and promptly disabled all constraints. Maybe they planned to go back and do the data cleansing afterwards, maybe not, but it was never done.

All told we found that this had been going on for about a year, and that some months the process to load the files had been executed 2-5 times. It was not \*terribly\* hard to clean up the data so that constraints could be re-enabled, the hard part was documenting everything that had happened in excruciating detail for the client. What was terribly hard was rewriting the DTS packages, because I needed to create staging areas for each of 14 pairs of tables, and write queries to enforce different product specific business rules on each pair. 

At the end of the ordeal (it took about a month to resolve completely) we were in pretty good shape. Load times for the monthly files went from days to minutes, and we never had the data integrity issues again. Looking back it was a good experience, because it taught me to never compromise when it comes to data integrity (and at the earliest possible point of my career). But for a few weeks there it was hell!

I guess I will pass the baton to [Mark Smith][7], I&#8217;m sure that he&#8217;ll have some good ones

 [1]: http://chrisshaw.wordpress.com/2008/12/09/sql-quiz-part-2-2/
 [2]: http://itknowledgeexchange.techtarget.com/sql-server
 [3]: http://sqlblog.com/blogs/denis_gobo/archive/2008/12/09/10409.aspx
 [4]: /index.php/ITProfessionals/ProjectManagement/sql-quiz-toughest-challenges
 [5]: /index.php/ITProfessionals/EthicsIT/an-ego-will-only-hurt-you-toughest-chall
 [6]: /index.php/DesktopDev/MSTech/challenges
 [7]: http://aspnetlibrary.com/