---
title: 'T-SQL Tuesday 32: A Day in the Life a Grrl Geek'
author: Jes Borland
type: post
date: 2012-07-17T10:28:00+00:00
ID: 1673
excerpt: 'It’s T-SQL Tuesday! This month’s topic, A Day in the Life, is hosted by my friend Erin Stellato (blog | twitter).  She wanted each of us to track what we do for a day. I can attest that she will ask you, "What do you do?", earnestly – she did when I wen&hellip;'
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/t-sql-tuesday-32-a/
views:
  - 105856
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server Admin

---
[][1]

<p style="text-align: center;">
  <img src="http://sqlblog.com/blogs/argenis_fernandez/TSQL2sDay150x150_thumb_2AA4EA0F.jpg" alt="" />
</p>

It’s T-SQL Tuesday! This month’s topic, A Day in the Life, is hosted by my friend Erin Stellato ([blog][2] | [twitter][3]).  She wanted each of us to track what we do for a day. I can attest that she will ask you, “What do you _do_?”, earnestly – she did when I went to the 2011 Columbus, OH SQL Saturday.

What do I _do_? As of May 8, 2012, I’m a Consultant with Brent Ozar PLF. Consultant. One of _those_ people. Yes, I am one! I perform health checks on client servers, recommend changes, and do training. I also get to blog and present a lot (which I love). Thankfully, Erin asked me to capture a fairly typical day.

7:30: Begin commute

7:30:30: Arrive in the office. I work from home, and it has its challenges, but I love it.

 

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/my office.jpg" alt="" />
</p>

I start by reading through my email and saying hello to all my friends Twitter. I need Twitter, now that I work from home. It gives me virtual “coworkers” to talk to.

7:45 – Begin research time. I’m doing a server health check with a client this week. We’ve been asked to review his delete process and recommend possible alternatives. I’m investigating a SQLCAT blog on Fast Ordered Deletes &#8211; <http://sqlcat.com/sqlcat/b/msdnmirror/archive/2009/05/20/fast-ordered-delete.aspx>.

8:00 – I begin putting together findings for the client in a PowerPoint deck.

8:40 – Start call with client.

Today we are covering re-indexing his most-used table. We script the create statements for a few nonclustered indexes, the drop statements for them, and the create statement for a new, shiny, non-clustered index.

We talk about his index maintenance strategy. It appears that at one point in time he had Ola Hallengren’s script up and running in Agent but it was failing. He’s going to investigate this.

Then, there are training opportunities. I talk through SQL Server Agent and automation, what is tempdb and what is it used for, and query patterns and parameter sniffing.

11:40 – The call is over. I go through my email to see what I’ve missed in the morning.

11:50 – I go to lunch with a friend. This is another work-from-home necessity. I need to have people to chat with on a regular basis.

1:00 – Return home

1:01 – It’s time to read email and blogs. Brent links to a story about [a company who pays for their employees to go on vacation][4]. I joke that we need a new policy.

1:15 – I’m working on the client findings PowerPoint deck. I review it with a co-worker to determine future strategy for them. Since I’m still fairly new to this consulting thing, there are things I want to confirm before I recommend them, based on the size of the company and the user’s level of familiarity with SQL Server.

2:00 –I’ll be presenting for the [QCPASS][5] user group later in the day, so I test connecting to LiveMeeting with one of the chapter leaders. This is important for any remote presentation – I want to make sure they can see me and my slides, and hear me.

2:15 – Back to the findings deck. I need to find information about SQL Server 2012 semantic search, and query tuning tips for the client.

3:00 – I need a brain break. I check in on my email, check twitter, and read a couple of blogs.

3:15 – I decide to move outdoors because it is BEAUTIFUL out. Now it’s time to work on query tuning information. The client had never seen an execution plan before Tuesday, and he’s excited to learn more about them. I grab [Grant Fritchey’s book][6] and get to work.

4:30 – BREAK TIME! I need to answer some emails, make a quick dinner, and get ready to present for a user group!

5:45 – 7:15 – I present “Make Your Voice Heard” for the Quad Cities PASS user group. This is one of my favorite presentations. I love energizing people. I love reminding them that they have information they can and should share with others!

I’m not done for the day, but I’m done for the day. I still need to finish up the findings slide deck for the client. I do that from about 9:00 – 10:00. Then it’s time to shut down the laptop and relax for a bit.

And that's what I _do_.

 [1]: http://erinstellato.com/2012/07/invitation-for-tsql-tuesday-day-life/
 [2]: http://erinstellato.com/
 [3]: http://twitter.com/erinstellato
 [4]: http://www.fullcontact.com/2012/07/10/paid-paid-vacation/
 [5]: http://qcpass.sqlpass.org/
 [6]: http://www.simple-talk.com/books/sql-books/sql-server-execution-plans/