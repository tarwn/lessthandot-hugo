---
title: SQL Friday, The Best SQL Server Links Of The Past Week Episode 10
author: SQLDenis
type: post
date: 2009-02-06T09:40:59+00:00
ID: 317
url: /index.php/datamgmt/datadesign/sql-friday-the-best-sql-server-links-of-10/
views:
  - 4369
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - sql friday

---
Here we are with another fascinating episode of the **SQL Friday, The Best SQL Server Links Of The Past Week** show. I can't believe it is week 10 already, if I continue like this I will be retired soon.....or just tired without the re prefix.
  
Here is what I found interesting this past week in SQL Land:

**[Shrinking is a popular topic...][1]**
  
Mike explains some concepts about shrinking and the transaction log

**[String comparison: binary vs. dictionary][2]**
  
Linchi Shea writes: t is well known that you get better performance if you compare two strings in a binary collation than in a dictionary collation. But is the performance difference significant enough to warrant the use of an explicit COLLATE clause in the string comparison expression?

**[Get a Quick Review of SQL Server Information][3]**
  
Allen White shows us how you can use PowerShell and SMO to get some information about your SQL Servers

**[Much ado about logins and SIDs][4]**
  
Greg Low explains what you have to do to SQL Server logins that need to be moved between servers

**[Back To Basics: Clustered vs NonClustered Indexes; what's the difference?][5]**
  
Denny Cherry highlights some differences between Clustered and NonClustered Indexes

**[What the heck is a SQL Server HOBT, anyway?][6]**
  
Aaron Alton explains what a HOBT aka Heap Or B-Tree is, so we are not talking about hobbits

**[T-SQL Bitwise Operations][7]**
  
Having written about this myself a while back here: http://sqlblog.com/blogs/denis_gobo/archive/2007/05/29/test.aspx
  
It was nice to see that Michelle Ufford did a nice job of explaining Bitwise Operations in T-SQL

**[Some Simple Code To Show The Difference Between Newid And Newsequentialid][8]**
  
Someone who thinks he is me 🙂 wrote a post showing why NEWID() is Evil

**[Index columns, selectivity and inequality predicates][9]**
  
Gail Shaw shows us how how selectivity and index column order affect inequality predicates.



That is it for this week, I will tag the weekly posts with **SQL Friday** in case you want to see the whole archive in the future

\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][10] forum or our [Microsoft SQL Server Admin][11] forum**<ins></ins>

 [1]: http://www.straightpathsql.com/blog/2009/1/31/shrinking-is-a-popular-topic.html
 [2]: http://sqlblog.com/blogs/linchi_shea/archive/2009/01/31/string-comparison-binary-vs-dictionary.aspx
 [3]: http://sqlblog.com/blogs/allen_white/archive/2009/02/01/get-a-quick-review-of-sql-server-information.aspx
 [4]: http://sqlblog.com/blogs/greg_low/archive/2009/02/02/much-ado-about-logins-and-sids.aspx
 [5]: http://itknowledgeexchange.techtarget.com/sql-server/back-to-basics-clustered-vs-nonclustered-indexes-whats-the-difference/
 [6]: http://thehobt.blogspot.com/2009/02/what-heck-is-sql-server-hobt-anyway.html
 [7]: http://sqlfool.com/2009/02/bitwise-operations/
 [8]: http://sqlblog.com/blogs/denis_gobo/archive/2009/02/05/11743.aspx
 [9]: http://sqlinthewild.co.za/index.php/2009/02/06/index-columns-selectivity-and-inequality-predicates/
 [10]: http://forum.lessthandot.com/viewforum.php?f=17
 [11]: http://forum.lessthandot.com/viewforum.php?f=22