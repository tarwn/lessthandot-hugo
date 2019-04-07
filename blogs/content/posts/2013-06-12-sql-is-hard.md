---
title: SQL Is Hard
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2013-06-12T19:04:00+00:00
ID: 2095
excerpt: "SQL is hard. Who's to say whether it's harder for the person that has no technical background or the one that is comfortable with object oriented, procedural, or functional styles and has to cross the great divide to set-based, declarative queries. In either case, there's a journey ahead of you."
url: /index.php/datamgmt/dbprogramming/sql-is-hard/
views:
  - 68989
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
tags:
  - sql development learning

---
SQL is hard. Who&#8217;s to say whether it&#8217;s harder for the person that has no technical background or the one that is comfortable with object oriented, procedural, or functional styles and has to cross the great divide to set-based, declarative queries. In either case, there&#8217;s a journey ahead of you.

Luckily there are a lot of great resources out there to help. From live webcasts like [SQLLunch][1] and [24 hours of PASS][2], to local [SQL Saturday events][3], from [recorded TechEd sessions][4] to [hundreds of books][5] to [blogs posts on sites like this][6]&#8230;.the list goes on.

But it&#8217;s still a long road, and building that initial solid foundation can be a hard step. 

## The Inquisitive Coworker

Over the years I&#8217;ve never had a good answer to the &#8220;where can I go to learn more about that&#8221; question from coworkers. There&#8217;s a ton of beginner books out there, but who has the time to weed through them and figure out which ones are more dangerous than they are helpful?

So the thought runs through my head, &#8220;How \_did\_ I learn that CROSS JOIN trick I just used?&#8221; and of course I don&#8217;t remember. Except I&#8217;m willing to bet that I learned it while trying to solve a problem. It might have been from a book, from a video, from trying someone else&#8217;s answer to one of Denis&#8217;s SQL puzzles. The driver was the fact that I had a problem to solve and that new tool I picked up was what helped me solve it. And I later remembered it, used it again, and learned more about it because it helped me solve a problem.

## Learn by Doing

I&#8217;ve always liked the idea behind the [TryRuby][7] site, an interactive experience that walks you through the basics of Ruby as you type them into an interactive console. Short of finding a mentor for every person that wants to give Ruby a try, this seems like an excellent way to provide a guided experience and help you build that initial foundation.

Why not do this with SQL?

And why not take it even further? After all, if you have an interactive site that helps build some basic skills with SQL, why can&#8217;t it also offer a section for more advanced techniques? Sure, you can download a free version of SQL Server Express or one of the other brands of SQL and try to learn these things on your own, but a guided set of exercises on a topic might be the perfect foundation before trying to branch out and try to invent your own problems to solve.

And thus was born the idea of [SQLisHard.com][8].

<div style="text-align: center">
  <img src="http://www.sqlishard.com/Content/Screenshot.png" alt="SQLisHard Screenshot" />
</div>

## It&#8217;s Still Early

It&#8217;s still in the very early stages with only a single set of exercises, and I&#8217;ve only implemented about half of that set. There&#8217;s a longer wishlist of features on a Trello board, but the initial focus here was making the core experience work. It&#8217;s not as whimsical as the TryRuby site (Damnit Jim, I&#8217;m a software engineer not a web designer). Most of the fanciest features are focused on helping me choose the right improvements to make; easy deployment, anonymous usage statistics, error capturing and reporting, and so on.

My list of &#8220;features I haven&#8217;t gotten to yet&#8221; starts with &#8220;finish the first exercise set&#8221;. After that I have more exercise sets, both foundational and &#8220;where can I go to learn that&#8221; advanced sets, an ability to save your progress (register/login) while continuing to let people without pre-registering (or without registering at all, up front registration annoys me), more permanent pieces for the few that I took shortcuts on, alternative exercise goals so you can try several queries while learning a single step, performance improvements and retry logic 

But most importantly, I&#8217;m looking for feedback. Does this seem like something you would point a beginner to? When your coworker asks where they could learn to use windowing functions, could you imagine a method like this being useful?

Check it out: [SQLisHard.com][8]

_The technologies behind both the site and the deployment process were interesting, I&#8217;ll have follow-up posts on those soon as well._

 [1]: http://www.sqllunch.com/ "SQLLunch"
 [2]: http://www.sqlpass.org/Events/24HoursofPASS.aspx "24 Hours of PASS"
 [3]: http://www.sqlsaturday.com/ "SQLSaturday"
 [4]: http://channel9.msdn.com/Events/TechEd/NorthAmerica/2012?sort=sequential&direction=desc&term=&t=database%2B-and-%2Bbusiness%2Bintelligence "TechEd 2012 Database Sessions"
 [5]: http://www.amazon.com/s/ref=nb_sb_noss_1?url=search-alias%3Daps&field-keywords=SQL "SQL Books on Amazon"
 [6]: /index.php/DataMgmt/ "Database posts at LessThanDot"
 [7]: http://tryruby.org "TryRuby interactive site"
 [8]: http://www.SQLisHard.com