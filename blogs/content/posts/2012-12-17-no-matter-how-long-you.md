---
title: 'SQL Advent 2012 Day 17: No matter how long you are on the wrong path, go back'
author: SQLDenis
type: post
date: 2012-12-17T22:14:00+00:00
ID: 1864
excerpt: This is day seventeen of the SQL Advent 2012 series of blog posts. Today we are going to look at what happens when you get stubborn and keep going down the wrong path
url: /index.php/datamgmt/dbprogramming/no-matter-how-long-you/
views:
  - 6733
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
This is day seventeen of the [SQL Advent 2012 series][1] of blog posts. Today we are going to look at what happens when people become stubborn and somehow attached to their code.

## If you are in a hole, stop digging!!!

[<img src="http://farm6.staticflickr.com/5152/6926071570_e2b5b849da_t.jpg" width="100" height="100" alt="no u turn sign from pro source inc third party logistics support" style="float:left;margin:0 5px 0 0;" title="Turn around before it is too late...." />][2]Somehow we as programmers and developers get sometimes emotionally attached to the code and applications that we create. We think of them as our little babies, it is like we see them grow up, we see the first time they start up and print a line or do an update statement, before you know it they manipulate the whole database and are responsible for some major business revenue. Unfortunately in real life you get one change to steer your kids into the right direction and helping them make correct choices. Don&#8217;t get attached to your code. If you see problems with it, drop a class or two, delete what doesn&#8217;t work, it is okay&#8230;the compiler won&#8217;t hate you or talk back to you (unless of course you count error messages and compile errors as talking back)

Another issue is that when we have worked on some code for 30 hours, and it doesn&#8217;t work just right, instead of scrapping it and starting from scratch we feel like if we just tweak it for one more hour it will work. Of course we all know that one more hour becomes a lot longer.

[<img src="http://farm3.staticflickr.com/2800/4435981810_07c6877ab2_m.jpg" width="240" height="192" title ="Digging a hole, reaching water but not resolving any problems" alt="Digging a hole, reaching water but not resolving any problems"style="float:left;margin:0 5px 0 0;" />][3]

Here is something that happened to me a while back, someone gave me some FoxPro code and told me to translate it into T-SQL. I started with a literal translation, this took me maybe half a day but then the results were not quite the same, I then wasted another two days or so on trying to fix it. In the end I gave up, deleted all the code, started from scratch without looking at the code at all and managed to write a complete solution in about 2 hours. My failure here was to realize when to stop making that hole that I was in deeper and deeper. Somehow I felt like that I had to make this code that I already worked on for two days work. This is crazy talk, I should have realized this much sooner, the approach I was taking was wrong. I was thinking that the easiest would be just to translate the code since the logic has already been tested. This is of course completely wrong as well, just because the logic works in the code that was given to me is no guarantee that it will still work once I am done with the translation.

When you get stuck and start trashing, just step away from the code, go for a walk, play a game, do something else. When you come back hopefully your mind is clearer, now think about what you want to accomplish and decide if your original approach is still valid.

That is all for day seventeen of the [SQL Advent 2012 series][1], come back tomorrow for the next one, you can also check out all the posts from last year here: [SQL Advent 2011 Recap][4]

 [1]: /index.php/DataMgmt/DBProgramming/sql-advent-2012-here-is
 [2]: http://www.flickr.com/photos/78985775@N07/6926071570/ "no u turn sign from pro source inc third party logistics support by prosourceinc, on Flickr"
 [3]: http://www.flickr.com/photos/gizmoni/4435981810/ "Digging a hole -Reaching water by mickrobi, on Flickr"
 [4]: /index.php/DataMgmt/DataDesign/sql-advent-2011-recap