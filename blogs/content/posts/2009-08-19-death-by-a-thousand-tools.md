---
title: Death by a thousand tools
author: Ted Krueger (onpnt)
type: post
date: 2009-08-19T11:09:48+00:00
ID: 542
url: /index.php/datamgmt/dbadmin/death-by-a-thousand-tools/
views:
  - 13020
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - IBM DB2 Admin
  - Microsoft SQL Server Admin
  - MySQL Admin
  - Oracle Admin

---
One thing these days that there is not a shortage of is tools to monitor, tune and process our database servers. We have all the big wigs providing you with dozens of packaged utilities that ensure everything you have worked hard for stays where it is and meeting your hard 99.999999% availability goals. So is that a good thing? Obviously using these tools are a good thing and they make DBAs look that much better with real-time proactive performance tuning and preventative actions before issues are even issues. The one thing I think it bad with all these bells and whistles is the concept of, "you can never have too many of them". Remember that [IBM commercial][1] with the thousands of old dust collecting servers? 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//ibm_com.gif" alt="" title="" width="477" height="237" />
</div>

Even knowing I'm not a blade fan and the mainframe commercial still makes me laugh, that marketing plan was spot on not only with servers but all aspects to the environment. 

When you decide your database server landscape has become a true animal in itself and the work that you put into simple monitoring efforts daily is overwhelming, you will start your search for the all saving tool that you can open every morning and simply look for green or red lights. I've tried to pass to others that these tools are a must and the cost is minimal to the results. There is a catch though. Let's think about the 5 hour energy drink prone, coffee crazed DBA that goes hog wild on downloading tools to do all sorts of things. They grab a monitor, a disk analyzer, an OS monitor, network monitor, fragmentation monitor, native tools add-ins up the \*** and basically a tool per though that comes to mind. Now let's look at the morning routine. They click 300 icons on their desktop to open all of these tools. They see green lights here and red lights there and blue and yellow spots in their vision because they've come to a point the tools that were so cool and freed all that time up for them to actually get some work done have become the actual work itself. Then they spend the next 5 hours maintaining the tools themselves. 

It's obvious and all experienced professionals will know that even too much of a good thing can make your life harder. Most people have come to know that I love automation. It's my bread and butter in the way I meet any new landscape or new task. I enjoy the fact any given instance the company I work for and has entrusted me with keeping a heart beat will tell me the millisecond it has issues with any aspect to the ability of serving the customer with the data it holds so dear to its heart. In order to do that and get yourself to a point you can successfully say that will happen, you have to take preparation and planning into the automation itself. This goes with the third parties you come to love so dearly. When you decide it is time the company needs to invest in monitoring tools and others that automate administration or simply make it a more real-time proactive setting, you have to do just that in taking time to plan it out. The thing you need to avoid is getting to the point in which the tools that are supposed to be helping you do not become a job in themselves for maintenance. That means, before you down 30 different tools, put great thought into which one can achieve all the tasks that the 29 in reality perform so you cover your bases with minimal effort in the tool itself. 

Now there is no one tool to rule them all. You'll have a few that you call your friend and there is always some maintenance that goes along with having those tools in your bed with you. My hope in reading this is that you'll think about it before you download a dozen things that in reality all do the same thing but because that one has a pretty picture or this one can blast so many emails you end up on the blacklist, you actually stop to decide which of those is better or will really help you do your job. The last thing you want to fall into is having a landscape of tools and not database servers. So when you plan your purchase put effort forth in planning what you have in your landscape already and what you really want to get out of a "master" tool. Ever landscape is similar but very different. An [Idera SQL Diagnostic Manager][2] may be perfect complimented by Red Gates, [SQL Compare bundle][3]. At the same token [Quest Spotlight][4] with some [Litespeed][5] and [Capacity Manager][6] may give you the upper hand. Those then possibly all combined or mismatched then give you minimal needs for maintaining them or expanding them. One thing that is a good idea to do is listen to others on what they are using but never make a decision just because some MVP is using this or that. It doesn't mean it fits in your personality and manner of maintaining things along with the configurations of the landscape you have created.

Some of the things that come to mind to think about when deciding which tools fit in the environment the best are

  * Does it require system object on a repository or all servers
  * Does it require features that typically I would cringe on enabling
  * Does it integrate seamlessly into my personal network infrastructure? 
  * Is it portable
  * How much maintenance will it in reality add to my job
  * Most of all, will it affect my instances negatively
  * Cost weighed against the amount of individual installations
  * Reporting for yourself and upper management
  * Can it make me better and not dumber? This means if the tool itself goes down, what effort is it to manually do the tasks or have another do them
  * How robust is the notifications and alerting abilities.
  * Reviews of the product

These rolled off just while writing this. You've probably gotten the point that it's not just a matter of downloading a trial version of something along with becoming its slave in doing exactly what it needs you to do in order to get it to work simply because it gives you cool dashboard pictures. That doesn't make it right for your instances. It really is as complex implementation and requires planning as does implementing any software package into your environment.

I'm not going to plug anything that I use personally in this but if you are interested in landscape to what I use go ahead and comment or better yet, start up a thread in the [DB related forums][7].

 [1]: http://www.youtube.com/watch?v=F63tYLhiqZ8&feature=related
 [2]: http://www.idera.com/Products/SQL-Server/SQL-diagnostic-manager/
 [3]: http://www.red-gate.com/products/sql_bundles/SQL_Comparison_Bundle.htm
 [4]: http://www.quest.com/spotlight-on-sql-server-enterprise/
 [5]: http://www.quest.com/litespeed-for-sql-server/
 [6]: http://www.quest.com/capacity-manager-for-sql-server/
 [7]: http://forum.ltd.local/viewforum.php?f=22