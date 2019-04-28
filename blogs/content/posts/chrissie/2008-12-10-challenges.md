---
title: Challenges
author: Christiaan Baes (chrissie1)
type: post
date: 2008-12-10T14:16:45+00:00
ID: 245
url: /index.php/desktopdev/mstech/challenges/
views:
  - 2326
categories:
  - Microsoft Technologies

---
[Chris Shaw posted a new SQL Quiz][1] where he asks: &#8220;What are the largest challenges that you have faced in your career and how did you overcome those?&#8221;

[Denny Cherry (@mrdenny on twitter)][2] tagged [SQLDenis][3] who tagged [George][4] who tagged [Ted][5] who then tagged me so here is my story:

And yeah I just copied the above from someone else&#8217;s post and improved it a little. I hate these tagging things. But since George and Ted could kill me if I don&#8217;t, I will anyway.

## 1) The Guid mistake.

When I redesigned my system and the accompanying database, I decided to use Guids all over the place. It&#8217;s easier than integers because I don&#8217;t have to wait on the database to give me an ID. And I&#8217;m one of those guys that thinks an object should get a proper ID. So I tested the new system and everything ran fine. But like George, I didn&#8217;t test with a big enough dataset. And this one table has millions of rows and the primary key is on the guid because it has no other natural key, the two other fields are just nearly unique but can be the same. Anyway, a certain part of that system became slow and it was the part that, among others, pulled data from that table. 421 rows or multiples of that but never more then 10&#215;421. And yet it was slow as hell. Loading a record with, among other things, the 421 rows took seconds, while it used to take less then a second on the old system. It took me a while to find out that the primary key and paging was the problem. With the Guid, the 421 rows were in several pages now (even 421 pages), so it had to do a lot more disk-IO. Changing to an int identity solved the problem because now all the rows fit in one page or two. Easily. 

## 2) The nHibernate mystery.

For my school project, I had to write something and I decided to use some new stuff I didn&#8217;t know yet. Well that&#8217;s not entirely true. I knew the Java side of things because that was the main language we used at school and I decide to use all the same technologies but then the .Net versions. This was not fun since I could not get any help from the teacher and I got stuck several times, but I learned lots of neat stuff and I learned to read blogs from persons like [Oren Eini][6], [Jeremy Miller][7] and [Jeff Atwood][8]; I never asked for help but found out most things by myself and although this was very frustrating it was very rewarding too. But the most frustrating was that nobody read the 400 page &#8220;book&#8221; I wrote and no-one ever looked at the 200,000 lines of code I wrote for this project. They just listened to the presentation and watched the demo and saw that it was good. 

That&#8217;s it, for the rest I&#8217;m perfect ;-).

I will also tag 2 LTD members for whom (among others) I have much respect [AlexCuse][9] and [damber][10]

 [1]: http://chrisshaw.wordpress.com/2008/12/09/sql-quiz-part-2-2/
 [2]: http://itknowledgeexchange.techtarget.com/sql-server
 [3]: http://sqlblog.com/blogs/denis_gobo/archive/2008/12/09/10409.aspx
 [4]: /index.php/ITProfessionals/ProjectManagement/sql-quiz-toughest-challenges
 [5]: /index.php/ITProfessionals/EthicsIT/an-ego-will-only-hurt-you-toughest-chall
 [6]: http://www.ayende.com/Blog/
 [7]: http://codebetter.com/blogs/jeremy.miller/
 [8]: http://www.codinghorror.com/blog/
 [9]: http://forum.ltd.local/memberlist.php?mode=viewprofile&u=56
 [10]: http://forum.ltd.local/memberlist.php?mode=viewprofile&u=53