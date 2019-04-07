---
title: SQL Friday, The Best SQL Server Links Of The Past Week Episode 8
author: SQLDenis
type: post
date: 2009-01-23T14:09:29+00:00
ID: 295
url: /index.php/datamgmt/datadesign/sql-friday-the-best-sql-server-links-of-8/
views:
  - 3398
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - sql friday

---
Here we are with another superfunkycalifragisexy episode of the **SQL Friday, The Best SQL Server Links Of The Past Week** show
  
Here is what I found interesting this past week in SQL Land:

**[Geek City: Too Many Indexes!][1]**
  
Kalen Delaney shows us that each table in SQL Server 2008 can contain a maximum of 999 nonclustered indexes, and 1 clustered index. These include the indexes generated to support any PRIMARY KEY and UNIQUE constraints defined for the table.

**[Back To Basics: Heaps][2]**
  
The official Microsoft definition of a HEAP is &#8220;a table without a clustered index.&#8221; As simple as it can get. It doesn&#8217;t matter if the table has 0 or 10 non-clustered indexes, as long as there is no clustered index on the table, it&#8217;s called a HEAP. And let me say it another way &#8220;any table that is not associated with a clustered index is called heap&#8221;

**[How to ask a Forums Question][3]**
  
I enjoyed this post because I myself have to deal with people like this every day in forums

**[Index columns, selectivity and equality predicates][4]**
  
Gail Shaw writes &#8220;There’s a common piece of advice given about columns in an index key that says that the most selective column should go first. I’m not going to say that’s incorrect, because it’s not. The problem is that it’s often given without any explanation as to why the most selective column should go first, nor are the other considerations for index key order mentioned.

This can lead to misunderstandings like, in the extreme case, where one person after hearing that advice went and added the primary key column as the leading column of every single nonclustered index (because it’s highly selective), and then wondered why his database performance decreased dramatically.&#8221;

**[Breaking a String into &#8220;Words&#8221;][5]**
  
Aaron Alton writes: &#8220;Occasionally on the MSDN forums, someone has the requirement to break out a string into words, one word per line. The poster usually says “I have the requirement to break out a string into words, one word per line”.&#8221;

**[Breaking a String into &#8220;Words&#8221; the CLR way][6]**
  
Jonathan Kehayias uses the CLR to break a string into words

**[SQL Server 2008 Cumulative Update 3 is available][7]**
  
Aaron Bertrand lets us know that SQL Server 2008 Cumulative Update 3 is available

**[How It Works: SQL Server Sparse Files (DBCC and Snapshot Databases) Revisited][8]**
  
Sarah Henwood and Bob Dorr explain SQL Server Sparse Files

**[How It Works: sys.dm\_os\_buffer_descriptors][9]**
  
Answer to the question: I am counting pages in the buffer pool using &#8216;Select count(*) from sys.dm\_os\_buffer_descriptors&#8217; and I get 6,460 buffers but when when I look at the Buffer Node:Database pages counter it shows 6,599. Why the difference?&#8221;

**[RML Utilities &#8211; ReadTrace and how to workaround MARS][10]**
  
We&#8217;ve lately gotten several questions from users regarding ReadTrace and how to workaround when your input trace files contain MARS (Multiple Active Result Sets)

**[Viewing SQL Server Plan Guides][11]**
  
Denny Cherry explains how to view SQL Server Plan Guides



That is it for this week, I will tag the weekly posts with **SQL Friday** in case you want to see the whole archive in the future

\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][12] forum or our [Microsoft SQL Server Admin][13] forum**<ins></ins>

 [1]: http://sqlblog.com/blogs/kalen_delaney/archive/2009/01/18/too-many-indexes.aspx
 [2]: http://sankarreddy.spaces.live.com/Blog/cns!1F1B61765691B5CD!323.entry
 [3]: http://sqlblog.com/blogs/jonathan_kehayias/archive/2009/01/19/how-to-ask-a-forums-question.aspx
 [4]: http://feeds.feedburner.com/~r/SqlInTheWild/~3/516800275/
 [5]: http://feedproxy.google.com/~r/TheHobt/~3/Lk-YxdQ8qu4/breaking-string-into-words.html
 [6]: http://sqlblog.com/blogs/jonathan_kehayias/archive/2009/01/20/breaking-a-string-into-words-the-clr-way.aspx
 [7]: http://sqlblog.com/blogs/aaron_bertrand/archive/2009/01/20/sql-server-2008-cumulative-update-3-is-available.aspx
 [8]: http://blogs.msdn.com/psssql/archive/2009/01/20/how-it-works-sql-server-sparse-files-dbcc-and-snapshot-databases-revisited.aspx
 [9]: http://blogs.msdn.com/psssql/archive/2009/01/21/how-it-works-sys-dm-os-buffer-descriptors.aspx
 [10]: http://blogs.msdn.com/psssql/archive/2009/01/21/prb-rml-utilities-readtrace-and-how-to-workaround-mars.aspx
 [11]: http://itknowledgeexchange.techtarget.com/sql-server/viewing-sql-server-plan-guides/
 [12]: http://forum.ltd.local/viewforum.php?f=17
 [13]: http://forum.ltd.local/viewforum.php?f=22