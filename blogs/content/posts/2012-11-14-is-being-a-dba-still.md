---
title: Is being a DBA still sexy?
author: Jes Borland
type: post
date: 2012-11-14T11:46:00+00:00
ID: 1788
excerpt: 'I hear more and more emphasis on two things: big data and business intelligence. Companies are investing more and more money in both. Knowing this, are you, a DBA, thinking your job is threatened?'
url: /index.php/datamgmt/dbadmin/is-being-a-dba-still/
views:
  - 24860
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - IBM DB2 Admin
  - Microsoft SQL Server Admin
  - MySQL Admin
  - Oracle Admin

---
I hear more and more emphasis on two things: big data and business intelligence. Companies are investing more and more money in both. Conferences are providing more tracks on those topics. Books that focus on these topics seem to be everywhere. They were the focus of last week's keynotes at PASS Summit. Yes, there is a lot of data out there. Yes, mining the data the business has collected for analysis, insights, and trends is what keeps the business moving forward. These topics are sexy. They're attractive. People want to see them, and spend time with them.

Knowing this, are you, a DBA, thinking your job is threatened? Are you worried that you will get phased out with time, or relegated to a dark basement office, next to the server room? Does data seem unattractive, and, let's face it, unsexy? Do you fear becoming a [Milton][1]?

 

<p style="text-align: center;">
  <img src="http://cdn.ebaumsworld.com/2012/03/82378978/stapler.jpg" alt="" />
</p>

_Don't worry._ As long as there is data, there will be a need for a data steward; for someone to protect it and keep it accessible. As we've moved toward more computerized cars, have mechanics disappeared? No! They have had to change their approach and learn more about the computers they work with, but they are still changing oil and replacing timing belts.

If you want to keep enjoying your job, and be attracted to it, now is the time to start learning new features and facets of SQL Server. This will keep you fresh, because you will learn new things. It will keep you marketable, because companies are going to need these skills going forward. It will show your current or potential employers that you are willing to stay current, which is a valuable skill.

What are some ways to keep database administration sexy?

### Managing VLDBs (Very Large Databases)

As the amount of data we store continues to grow, you will need to know how to manage larger volumes. Consider new backup strategies. Investigate things like backup compression, striping across multiple files, and multiple filegroups. Want a lesson? Ask Bob Pusateri ([blog][2] | [twitter][3]) about his backup story.

There is more to administration than backups, of course. Consider other tasks like restoring backups, CHECKDB, and index maintenance. How are you going to handle each of these things? There are many new (or less well-known) strategies, such as CHECK FILEGROUP or custom index maintenance scripts that you could implement.

 

<p style="text-align: center;">
  <img src="http://2.bp.blogspot.com/-SuWyCTDJnJU/T17QTJys85I/AAAAAAAAGRw/-roZPgMTkH8/s1600/very+large+tea+mug.jpg" alt="" />
</p>

<p style="text-align: center;">
  <em>Your database is like my coffee cup: LARGE</em>
</p>

### Indexing

There is a new world of data out there – XML, spatial, and flat out large amounts of data. This data needs indexing, too. Do you understand how spatial data is stored, and how indexing can help it? (If you don't, be sure to ask Ted ([blog][4] | [twitter][5]) for his explanation.) Do you know how to retrieve XML data and what to index on it? What are strategies to help retrieve large amounts of data, such as filtered indexes and columnstore indexes with xVelocity technology? xVelocity is sexy. It even sounds cool. All of this and more will be more prevalent in the coming years.

### Storage

As the volume of data grows, a physical SQL Server instance can outgrow attached, dedicated disk. We now work in environments with terabytes of data in SANs, which are shared between multiple servers, which are used by multiple virtual machines and hosts. Understanding the architecture of these systems, and how all the pieces communicate and work together, will be a vital part of the DBA job going forward.

<p style="text-align: center;">
  <em><img src="http://www.oppictures.com/singleimages/400/54477.JPG" alt="" /><br /></em>
</p>

<p style="text-align: center;">
  <em>How many of these would it take to store your data?</em>
</p>

### Look Forward

By realizing that the world of data is changing and committing to change with it, you are positioning yourself to have a successful future.   Start researching the future of data. Think about what you are interested in learning about the future. Begin studying and applying what you're learning. You're going to be attracted to your job again. It's going to be sexy to be a DBA for years to come!

 [1]: http://www.imdb.com/character/ch0001900/
 [2]: http://www.bobpusateri.com/
 [3]: https://twitter.com/SQLBob
 [4]: /index.php?disp=authdir&author=68
 [5]: https://twitter.com/onpnt