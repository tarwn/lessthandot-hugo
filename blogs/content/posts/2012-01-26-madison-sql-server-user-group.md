---
title: Madison SQL Server User Group
author: Ted Krueger (onpnt)
type: post
date: 2012-01-27T00:05:00+00:00
ID: 1507
excerpt: "I had the distinct pleasure of attending my first MADPASS meeting last night, January, 25th.  The drive to the Madison SQL Server User Group from the Illinois, Wisconsin border wasn't that bad and well worth it.  I was able to catch up with the Grandfat&hellip;"
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/madison-sql-server-user-group/
views:
  - 3257
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server Admin

---
<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/logMadPass_591x275_3.jpg?mtime=1327628879"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/logMadPass_591x275_3.jpg?mtime=1327628879" width="350" height="163" align="left" /></a>
</div>

I had the distinct pleasure of attending my first [MADPASS][1] meeting last night, January, 25<sup>th</sup>.  The drive to the Madison SQL Server User Group from the Illinois, Wisconsin border wasn't that bad and well worth it.  I was able to catch up with the Grandfather of SSIS, [Andy Leonard][2].  Andy was in Chicago the same week of the meeting and was nice enough to speak at the meeting.  Andy is one of the best presenters I know and enjoy sitting in his sessions more than most.  He has a perfect is of highly adapt skills that he shares openly, a great sense of humor and an all-around, extremely comfortable presence in a room as a presenter.  In my own experience, there are few presenters that can create a comfort level between the attendees and the speaker so quickly as Andy can do.  That to me is the formula for a successful presentation along with a vast knowledge of the topic and respect around the world for it.

As well as getting to catch up with Andy, [Aaron Lowe][3], [Jes Borland][4] and many other SQL Server peeps were in attendance.  Having this kind of group all in the same room just screams SQL SERVER GOODNESS!!!

There were several announcements during this meeting. Aaron Lowe talked about [SQL Friends][5]. A creation of Aaron's that I am really excited about. This will give myself and others a perfect opportunity to sit with other SQL Server professionals in a relaxing setting and just talk SQL Server. Head to the site and get ready for the first meeting coming soon!

SQL Saturday Everywhere! Wisconsin and Chicago are putting on SQL Saturday events in the spring.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-109.png?mtime=1327629592"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-109.png?mtime=1327629592" width="400" height="249" /></a>
</div>

As well as attending, I was able to present on Merge Replication.  This was the second time I was able to present his session.  I feel this time really fine-tuned it and from the responses, the group enjoyed learning some cool things to make Merge Replication and offline mobility of data.  Having the ability to take an application that can only function while retaining some type of online presence and sever that connectivity while retaining full functionality, is truly propelling it to the next level.  Merge Replication while coupled with skills in-house and a well-designed publication and landscape behind it, can be a stable and sound solution to "once and awhile connectivity" and keeping data in sync.

Some high points that we covered were

  * Designing publication – one publication or multiple
  * Snapshots and partitioned snapshots
  * Hardware on a subscriber level and some discussions over what to expect for server resources
  * Parallel replication against multiple publications
  * Conflict Resolution with COM (column level tracking) and Business Logic 
  * Indexing, DR and HA for Replication
  * A really cool SSIS package to run soon-to-expire subscribers, prepares snapshots, compress and ship them out to remote shares.
  * Some short discussion on Sync Framework
  * And not a whole lot of discussion on the Cloud

Overall we talked about a lot of topics and still have so much more we could talk about.

I really hope everyone did enjoy Andy and my presentations.  I know for my part, it was a really great night of SQL Server, making new friends and catching up with old friends.  I'm looking forward to coming back in March!

You can download the deck using this link, [Merge Replication for Offline Data Mobility.pdf][6].  I will make the code that went with my presentation available soon.  Doing some clean up because of that developer that was making me sweat it in the front row.  😉

 [1]: http://www.madpass.org/
 [2]: http://sqlblog.com/blogs/andy_leonard/
 [3]: http://www.aaronlowe.net/
 [4]: /index.php/All/?disp=authdir&author=420
 [5]: http://sqlfriends.org/
 [6]: /wp-content/uploads/blogs/DataMgmt/Merge%20Replication%20for%20Offline%20Data%20Mobility.pdf?mtime=1327628730