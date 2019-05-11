---
title: 'Friday SQL Nugget #1'
author: Ted Krueger (onpnt)
type: post
date: 2012-01-13T12:56:00+00:00
ID: 1488
excerpt: |
   
  
  I'm going to try to start a new Friday post here on LessThanDot called, SQL Nuggets.  Yes, I said Nuggets!  It's commonly known that posting technical content on a Friday is less effective than publishing between Tuesday and Thursday.  The simple f&hellip;
url: /index.php/itprofessionals/professionaldevelopment/friday-sql-nugget-1/
views:
  - 2981
rp4wp_auto_linked:
  - 1
categories:
  - Professional Development

---
 

<div class="image_block">
  <span style="font-family: verdana,geneva;"><img src="/wp-content/uploads/blogs/ITProfessionals/sqlnugget.jpg?mtime=1326466147" alt="" width="400" height="400" align="left" /></span>
</div>

<span style="font-family: verdana,geneva;">I'm going to try to start a new Friday post here on LessThanDot called, SQL Nuggets.  Yes, I said Nuggets!  It's commonly known that posting technical content on a Friday is less effective than publishing between Tuesday and Thursday.  The simple fact that most readers will not be in a position to read RSS feeds is there.  However, Friday still has a relaxing feel to it and SQL still has something to offer us in the form of a valuable but short post.</span>

<span style="font-family: verdana,geneva;">On that note, this Friday's nugget will be, "<strong>Deciding I need to delete it all and start over again</strong>".  In any corner of technology, we reach a point in gaining experience where we swallow our pride and chuck days of hard work in the garbage.  We realize we must do this because the path we've taken to meet an expectation or objective has taken us down a messy road of poor performance, messy maintainability and downright, poor quality.  This nugget exposes a story of that exact sequence of events.</span>

<span style="font-family: verdana,geneva;">You may think I'm going to tell you about something I did years ago when I was just starting out in IT.  The fact is, this story comes from this week.  Yes, this concept of realizing we need to just delete whatever we are working on and start over never goes away in a career.  This week I started writing a new, fresh SSIS package to pull statistical information from a listing of SQL Server instances.  The package was extremely easy as far as concept goes.  Pull index analysis information, the plans in the cache, the plans themselves and other related information, connection information and so on.   I decided to execute a series of DMV related queries and slam them into a variable object in SSIS.  This way I could simply pull the information and get it off the servers.  That would leave a smaller footprint.  The task is actually common.  Shredding variables that are object types with a Foreach Loop Container and inserting the data where you need it.  Well, the task is useful mostly when you want to manipulate each row.  This type of shredding is a great row-by-row operation.  I wrote the package, which contained nearly 40 variables to support the entire method, tested it on my local instance and all worked well.  At this point I did start to think, "This may not be so good".  Thinking about a real database, and a high number of indexes for one, made me rethink the process.  So I ran the package on a server that would show some real performance statistics from the packages execution.  Yes, after about 40 minutes, I killed the still-running package.</span>

<span style="font-family: verdana,geneva;">So I deleted package 1.  Threw it to the curb even knowing I had little over hours to complete this task.  I did what I should have and used my brain and skills in SSIS.  I replaced the variables with basic data flow tasks and transformations.  I used another common method to handle my use of temporary tables; Execute SQL Task to Data Flow with a unique connection manager using the keep connection option true.</span>

<div class="image_block">
  <span style="font-family: verdana,geneva;"><a href="/media/blogs/ITProfessionals/-11.png?mtime=1326465329"><img src="/wp-content/uploads/blogs/ITProfessionals/-11.png?mtime=1326465329" alt="" width="200" height="200" align="left" /></a></span>
</div>

<span style="font-family: verdana,geneva;">Package 2 executed on three instances with dozens of databases, ranging in size from 10GB to 300GB, in a few seconds.</span>

<span style="font-family: verdana,geneva;">Now, package 1, wow, it was cool.  It had all the cool tasks, script components, data flows in and out and all around.  Manipulated all the data in memory so the job server did the work and the database servers did what they do, serve data.  But wow, was it slower than a Renault Alliance going up a 30% sloped hill. Package 2 turned out to be well formed, stable,  of higher quality, and was as fast as the Renault alliance rolling down that same hill.</span>

<span style="font-family: verdana,geneva;">Even if something works and it's doing the job that is needed, realizing it needs to be completely trashed and starting from scratch does have value in how your work is viewed.  I hope you've come to this point in your career, have been in the same situation, and have done the same thing.  It is a great part of growing and gaining the experience we need to stay on top.</span>

<span style="font-family: verdana,geneva;">On this first post, I'm tagging Jes Borland (<a href="http://twitter.com/grrl_geek">Twitter</a> | <a href="/index.php/All/?disp=authdir&author=420">blog</a>), George Mastros (<a href="http://twitter.com/gmmastros">Twitter</a> | <a href="/index.php/All/?disp=authdir&author=10">blog</a>), Denis Gobo (<a href="http://twitter.com/denisgobo">Twitter</a> | <a href="/index.php/All/?disp=authdir&author=4">blog</a>), Hope Foley (<a href="http://twitter.com/hope_foley/">Twitter </a> | <a href="http://hopefoley.wordpress.com/">Blog</a>) and Christiaan Baas (<a href="http://twitter.com/chrissie1">Twitter</a> | <a href="/index.php/All/?disp=authdir&author=7">blog</a>).</span>

<span style="font-family: verdana,geneva;"><br /></span>