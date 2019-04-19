---
title: 'Go ahead and click it!  I triple dog dare you!!!'
author: Ted Krueger (onpnt)
type: post
date: 2009-07-10T10:56:32+00:00
ID: 501
excerpt: "As a DBA the word no comes out of us often.  It's just the nature of our jobs that we will find ourselves not accepting requests and revoking bad choices already made.  If you are a DBA and you're a yes type of person then you're in the wrong field.  Pr&hellip;"
url: /index.php/datamgmt/dbprogramming/go-ahead-and-click-it-i-triple-dog-dare/
views:
  - 20652
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
As a DBA the word no comes out of us often. It's just the nature of our jobs that we will find ourselves not accepting requests and revoking bad choices already made. If you are a DBA and you're a yes type of person then you're in the wrong field. Protecting the company heart and soul in the form of data cannot be successful with the word yes. It's just not possible. 

Now what do you do when something happens out of your control? My first thought is, I hope you handled the situation well enough to prevent it in the first place. Here is a perfect example that happened recently. An email comes in stating one of the analysts needs permissions on a critical production database server. Permissions for what you say? Well this is the error they had attached

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//error_1.gif" alt="" title="" width="754" height="152" />
</div>

Yes, they tried to run a trace on the production server. I hope the thought of that scares the hell out of you as a DBA. This leads into why the analyst even had the option of opening profiler. Well, they need SQL Tools in order to manage simple reports out of the test systems. This is all very locked down on security even in the test systems and only specific rights are allowed given the position they are in. Yes, you should lock down your test and dev systems as well. If we took a poll on time spent troubleshooting production vs. test instances, my money would be on test taking the most time of your day. So why allow more havoc to be run on those servers when you can prevent it as much as you do in production. Needless to say the analyst had to have tools installed in order to perform their job. That's understandable of course. SQL tools installations fly all around a normal IT group and we all see little thought going into if we should be installing them. So what's the issue then? If they need it, why am I writing this? Well, I want to talk about the red button in front of my mother's face. See, my mother can't help herself from pushing that button. No matter how hard she tries, that damn button is getting pushed. One time she pushed the wrong button and paid the price. I can't go into details. She's my mother and I love her dearly and want to protect her from embarrassment. But, well...

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//triple-dog-dare.jpg" alt="" title="" width="400" height="267" />
</div>

Should common sense of not knowing the consequences of that button being pushed have played an important role in not pushing it? Umm...yeah! So when the analyst had whatever support step that showed them the sweet path to open SQL Server Profiler, should they have even got to the point of clicking it? Honestly, I would hope not but that didn't happen here. They clicked away. 

Now how do you fix this problem after the damage is made? Probably around these lines...

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//fixinf_it.gif" alt="" title="" width="537" height="355" />
</div>

Let's take a look at the issue with that and what could have happened if they actually got that thing started. The options they chose were to write to a table and default events from Profiler. The table they were going to create and use was in the same database they were trying to mine out some code. Unfortunately they were not stopped at the table creation. This is due to horrible planning on the ERP systems side and having a database role that allows users to do just that. This database is part in log shipping and replication. First issue log growth. What happens when you insert a couple hundred thousand lines in a few minutes into a database? Now let's think about those log backups and sending them offsite to the other stand by instance. Not enough? How about IO when the log is growing and possibly the database, the CPU, Memory and other resources sucked down by the trace? Oh yeah, your mirror uncommit skyrockets and now you're vulnerable to failure and losing data even with your sweet high availability setup. Now you have 500 really angry users across your WAN and who are they calling? Here comes the Google search for truncate log files from the not so great DBA. 

\*sigh\* All this happened because someone clicked something they had no prior training or knowledge on when they should have questioned the concept of what the consequences of that action would have been.

I'm not sure if I need to say under any circumstances should you ever give someone other than a DBA sysadmin rights. If there is someone other than a DBA in that group then you may be so far in trouble that what I say may not help you. I'm guessing if you have non-DBA users in sysadmin roles or even worse, BUILTINAdmins in the sysadmin role, then the sa password is probably written on your cube wall as well. I guess my only plea with anyone that reads this is to think before you click something. If you are a systems administrator, think before you click that button that alters the way AD works. Think before you add that disk to your server midday and during peak business hours. Think before you click a program that you've never clicked before. And to my fellow DBAs, secure your database servers. You're the DBA. You should be the only one(s) with power to do those things. Prevent your users and peers from being able to get farther than this person did.