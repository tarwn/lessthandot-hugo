---
title: T-SQL Tuesday #21 – A trigger can do that!
author: Ted Krueger (onpnt)
type: post
date: 2011-08-10T18:04:00+00:00
ID: 1297
excerpt: "This month's T-SQL Tuesday Wednesday topic is all about failure.  To be exact, Crap Code.  I'm not going to post any code but instead post the problem, solution and coding initiative that were taken.  Code was the 'crap' part that in all was the failure&hellip;"
url: /index.php/datamgmt/dbprogramming/t-sql-tuesday-21-a/
views:
  - 43001
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
<div class="image_block">
  <a href="http://sqlblog.com/blogs/adam_machanic/archive/2011/08/03/t-sql-tuesday-21-a-day-late-and-totally-full-of-it.aspx"><img alt="" src="/wp-content/uploads/blogs/All/tsql_trig_1.GIF?mtime=1313006344" width="150" height="147" align="left" /></a>
</div>

<p class="MsoNormal" style="margin: 0in 0in 10pt;">
  <span style="font-family: Calibri; font-size: small;">This month's </span><a href="http://sqlblog.com/blogs/adam_machanic/archive/2011/08/03/t-sql-tuesday-21-a-day-late-and-totally-full-of-it.aspx"><span style="font-family: Calibri; color: #0000ff; font-size: small;">T-SQL Tuesday Wednesday topic is all about failure</span></a><span style="font-family: Calibri; font-size: small;">.<span style="mso-spacerun: yes;">  </span>To be exact, Crap Code.<span style="mso-spacerun: yes;">  </span>I'm not going to post any code but instead post the problem, solution and coding initiative that were taken.<span style="mso-spacerun: yes;">  </span>Code was the "crap" part that in all was the failure in this real-life event.<span style="mso-spacerun: yes;">  </span>For the record, the story I am about to tell everyone in this post, is a real-life story that I did early on in my career.<span style="mso-spacerun: yes;">  </span>Although it is my hope to always teach in order to prevent things like this particular failure to happen to all of you, I also have the belief that we learn from failure.<span style="mso-spacerun: yes;">  </span>Sometimes, a person just needs to have the reaction and heart pumping feeling from a complete failure that affects thousands of users, in order to harden how we need to approach anything.</span><a href="http://sqlblog.com/blogs/adam_machanic/archive/2011/08/03/t-sql-tuesday-21-a-day-late-and-totally-full-of-it.aspx"></a><em style="mso-bidi-font-style: normal;"><span style="line-height: 115%; font-family: Tahoma; font-size: 9.5pt;"> </span></em>
</p>

<p class="MsoNormal" style="margin: 0in 0in 10pt;">
  <strong style="mso-bidi-font-weight: normal;"><span style="line-height: 115%; font-family: Tahoma; font-size: 9.5pt;">The scenario...</span></strong>
</p>

<p class="MsoNormal" style="margin: 0in 0in 10pt;">
  <em style="mso-bidi-font-style: normal;"><span style="font-size: small;"><span style="font-family: Calibri;">For years the company has dealt with two systems that are completely disconnected but business continuity replies on the information of the systems to be compared, aggregated and reported on.<span style="mso-spacerun: yes;">  </span>You were just promoted to the sole .NET/Data Engineer in the company and quickly included on the project that will automate combing these data sources into one source for quick and efficient reporting.</span></span></em>
</p>

<p class="MsoNormal" style="margin: 0in 0in 10pt;">
  <em style="mso-bidi-font-style: normal;"><span style="font-size: small;"><span style="font-family: Calibri;">The first meeting is in session and you get all the requirements in front of you.<span style="mso-spacerun: yes;">  </span>As you see all the requirements you recall recently using a trigger for the first time to handle data in and data out of a table.<span style="mso-spacerun: yes;">  </span>Immediately you spout out, "We could do this in a trigger in a weeks' time".<span style="mso-spacerun: yes;">  </span></span></span></em>
</p>

<p class="MsoNormal" style="margin: 0in 0in 10pt;">
  <span style="font-size: small;"><span style="font-family: Calibri;"> </span></span><em style="mso-bidi-font-style: normal;"></em>
</p>

<div class="image_block">
  <a href="/wp-content/uploads/blogs/All/tsql_trig_2.GIF?mtime=1313006344"><img alt="" src="/wp-content/uploads/blogs/All/tsql_trig_2.GIF?mtime=1313006344" width="134" height="89" align="left" /></a>
</div>

<p class="MsoNormal" style="margin: 0in 0in 10pt;">
  <em style="mso-bidi-font-style: normal;"><span style="font-size: small;"><span style="font-family: Calibri;">The team doesn't even question you.<span style="mso-spacerun: yes;">  </span>You are the brain child for SQL Server and knowledge base for .NET as combined to anything that has Microsoft on it.<span style="mso-spacerun: yes;">  </span>See, the team is an SAP/Oracle team.<span style="mso-spacerun: yes;">  </span>They have no clue what the other side of the wall does or can do.<span style="mso-spacerun: yes;">  </span></span></span></em>
</p>

<p class="MsoNormal" style="margin: 0in 0in 10pt;">
  <em style="mso-bidi-font-style: normal;"><span style="font-size: small;"><span style="font-family: Calibri;">So you all agree on your recommendation.<span style="mso-spacerun: yes;">  </span>All is good in the data world and you set off to write the trigger.</span></span></em>
</p>

<p class="MsoNormal" style="margin: 0in 0in 10pt;">
  <span style="font-size: small;"><span style="font-family: Calibri;">I'm going to go over the way this project was attacked by non-other than, myself.<span style="mso-spacerun: yes;">  </span></span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 10pt;">
  <span style="font-family: Calibri; font-size: small;">I took about twenty minutes to find the table in System A to determine the table that held the data that I needed pulled out and inserted into another database.<span style="mso-spacerun: yes;">  </span>Once I had the table and the actual columns, I wrote a trigger.<span style="mso-spacerun: yes;">  </span>The trigger was lengthy due to some business logic that was required.<span style="mso-spacerun: yes;">  </span>The finished trigger ended up being around 300 lines of Grade-A Transact SQL and something to be proud of.<span style="mso-spacerun: yes;">  </span>All development server tests went smooth and it was ready for production.</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 10pt;">
  <strong style="mso-bidi-font-weight: normal;"><span style="font-size: small;"><span style="font-family: Calibri;">Going into Production</span></span></strong>
</p>

<p class="MsoNormal" style="margin: 0in 0in 10pt;">
  <span style="font-family: Calibri; font-size: small;">The trigger implementation into production was fairly easy.<span style="mso-spacerun: yes;">  </span>Execute the CREATE TRIGGER and wait for the data to start inserting into the new database.<span style="mso-spacerun: yes;">  </span>The script was executed and data started moving.<span style="mso-spacerun: yes;">  </span>Success!!!</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 10pt;">
  <span style="font-family: Calibri; font-size: small;">The next day a clerk started reporting some performance problems with saving new records from the application that the new trigger was on.<span style="mso-spacerun: yes;">  </span>I dismissed the trigger and started looking at other things.<span style="mso-spacerun: yes;">  </span>After all, the application was a web site.<span style="mso-spacerun: yes;">  </span>We all know web sites are slow.<span style="mso-spacerun: yes;">  </span>In fact, after getting all the users out and performing an IISRESET, the application was working fine again for normal searches.<span style="mso-spacerun: yes;">  </span>Success!!!!</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 10pt;">
  <span style="font-size: small;"><span style="font-family: Calibri;">Twenty minutes later the clerk tried the new record screen again and the problem in fact was not fixed.<span style="mso-spacerun: yes;">  </span>Moreover, some of the other clerks started receiving errors when saving with a very cryptic error message about transactions failing.<span style="mso-spacerun: yes;">  </span>The trigger finally came under review and I started to look into handling the odd transaction error in the trigger.<span style="mso-spacerun: yes;">  </span>It was obvious it was the trigger, due to a specific column being references in the error.<span style="mso-spacerun: yes;">  </span></span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 10pt;">
  <span style="font-family: Calibri; font-size: small;">I found XACT_ABORT and thought it is was a solution because it will roll back the entire transaction and not throw the ugly error.<span style="mso-spacerun: yes;">  </span>It works!<span style="mso-spacerun: yes;">  </span>Success!!!</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 10pt;">
  <span style="font-size: small;"><span style="font-family: Calibri;">It works for a little while and then piece-meal records are being inserted into the main systems database.<span style="mso-spacerun: yes;">  </span></span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 10pt;">
  <strong style="mso-bidi-font-weight: normal;"><span style="font-size: small;"><span style="font-family: Calibri;">The problems here</span></span></strong>
</p>

<p class="MsoNormal" style="margin: 0in 0in 10pt;">
  <span style="font-family: Calibri; font-size: small;">We all see where this is going so we'll stop and start to talk about this complete failure on my part.</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 10pt;">
  <span style="font-size: small;"><span style="font-family: Calibri;">First, should I have shouted out without giving much thought to the method of using a trigger?<span style="mso-spacerun: yes;">  </span></span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 10pt;">
  <span style="font-size: small;"><span style="font-family: Calibri;">Answer:<span style="mso-spacerun: yes;">  </span>Absolutely not!<span style="mso-spacerun: yes;">  </span>Simply because something is fresh in your mind does not mean it is appropriate for the next project.<span style="mso-spacerun: yes;">  </span>A skill learned is simply another skill in your arsenal.<span style="mso-spacerun: yes;">  </span>Each project should be approached with the mindset that it will require its unique method to achieve a successful implementation.<span style="mso-spacerun: yes;">  </span>Would a trigger be a possible solution?<span style="mso-spacerun: yes;">  </span>Possibly.<span style="mso-spacerun: yes;">  </span>However, given what I know now, do I even consider triggers?<span style="mso-spacerun: yes;">  </span>Not very often and we'll get into why not.<span style="mso-spacerun: yes;">  </span></span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 10pt;">
  <span style="font-family: Calibri; font-size: small;">Second, if you read closely, you would have caught this line, "<em style="mso-bidi-font-style: normal;">The trigger was lengthy due to some business logic that was required</em>".<span style="mso-spacerun: yes;">  </span>OK, what's the issue here?</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 10pt;">
  <span style="font-size: small;"><span style="font-family: Calibri;">Answer:<span style="mso-spacerun: yes;">  </span>If I could go back to 14-15 years ago, or whenever this real-life example happened, I'd kick my own ass.<span style="mso-spacerun: yes;">  </span></span></span>
</p>

<div class="image_block">
  <a href="/wp-content/uploads/blogs/All/tsql_trig_3.GIF?mtime=1313006344"><img alt="" src="/wp-content/uploads/blogs/All/tsql_trig_3.GIF?mtime=1313006344" width="323" height="184" align="left" /></a>
</div>

<p class="MsoNormal" style="margin: 0in 0in 10pt;">
  <span style="font-size: small;"></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 10pt;">
  <span style="font-size: small;"><span style="font-family: Calibri;">One of the most critical aspects to database designs I've learned is: Business Logic has no place in T-SQL and most of all at the transactional level.<span style="mso-spacerun: yes;">  </span>Databases are meant to store data.<span style="mso-spacerun: yes;">  </span>T-SQL is a set based language that is downright horrid at conditional logic or implementing rules based on the business.<span style="mso-spacerun: yes;">  </span>Implementing rules based on data is different.<span style="mso-spacerun: yes;">  </span>Defaults, constraints...all are good to manage the storage of data.<span style="mso-spacerun: yes;">  </span>The performance that came from this 300 line trigger based on the business logic completely drowned the applications speed and ability to insert new records on this particular table.<span style="mso-spacerun: yes;">  </span>That table happened to be the most critical table and starting point to an entire ticketing process.<span style="mso-spacerun: yes;">  </span>So we can see how having it function optimally was critical.<span style="mso-spacerun: yes;">  </span></span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 10pt;">
  <span style="font-family: Calibri; font-size: small;">Third, the entire project was a failure given the errors and problems that started appearing.<span style="mso-spacerun: yes;">  </span>Could this have been prevented even if a trigger was the answer?</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 10pt;">
  <span style="font-family: Calibri; font-size: small;">Answer: YES!!! We read that I tested in development.<span style="mso-spacerun: yes;">  </span>That was a good thing.<span style="mso-spacerun: yes;">  </span>We should test our work.<span style="mso-spacerun: yes;">  </span>However, I tested in development and simply inserted a record here and there with no other tests. <span style="mso-spacerun: yes;"> </span>Load testing was critical and would have shown the issues that came up after this was sent to production.<span style="mso-spacerun: yes;">  </span>There should have been a normal development, user acceptance testing and then training protocols put into place.</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 10pt;">
  <span style="font-family: Calibri; font-size: small;">Fourth (and worst): Was the use of XACT_ABORT to simply kill the transaction and roll it back to fix the nasty error the right thing to do?</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 10pt;">
  <span style="font-size: small;"><span style="font-family: Calibri;">Answer: XACT_ABORT may have been the option to set here to clean things up in the trigger but throwing it in and rolling that transaction back in the current transaction caused a massive data corruption problem.<span style="mso-spacerun: yes;">  </span>The application had started other transactions and had its own roll back process if something failed.<span style="mso-spacerun: yes;">  </span>So what I essentially did was trash the integrity of the database by doing this.<span style="mso-spacerun: yes;">  </span>I could have tested the use, validated the failure and data after that and then been satisfied.<span style="mso-spacerun: yes;">  </span></span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 10pt;">
  <strong style="mso-bidi-font-weight: normal;"><span style="font-size: small;"><span style="font-family: Calibri;">What I learned?</span></span></strong>
</p>

<div class="image_block">
  <a href="/wp-content/uploads/blogs/All/tsql_trig_4.GIF?mtime=1313006344"><img alt="" src="/wp-content/uploads/blogs/All/tsql_trig_4.GIF?mtime=1313006344" width="106" height="160" align="left" /></a>
</div>

<p class="MsoNormal" style="margin: 0in 0in 10pt;">
  <span style="font-size: small;"></span><span style="font-size: small;"><span style="font-family: Calibri;"><span style="mso-spacerun: yes;"> </span>I learned very quickly to keep my mouth shut when it comes to me "thinking" I have the answer.<span style="mso-spacerun: yes;">  </span>Discuss potential solutions and paths that can be reviewed as possible solutions.<span style="mso-spacerun: yes;">  </span>Research other possible solutions that may be in place already.<span style="mso-spacerun: yes;">  </span>Do the work and don't commit to something as a solution simply because you were excited you recently learned it.<span style="mso-spacerun: yes;">  </span></span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 10pt;">
  <span style="font-size: small;"><span style="font-family: Calibri;">And I beg all of you; test your work under the normal use that the user community is putting on it.<span style="mso-spacerun: yes;">  </span>The worst person to test a program is a programmer.<span style="mso-spacerun: yes;">  </span>This goes the same for databases.<span style="mso-spacerun: yes;">  </span>The worst person to test an applications use of a database and results is a database administrator or developer. <span style="mso-spacerun: yes;"> </span>They will typically just write an INSERT and run it without using the application.<span style="mso-spacerun: yes;">  </span></span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 10pt;">
  <span style="font-family: Calibri; font-size: small;"> </span>
</p>