---
title: SQL Friday, The Best SQL Server Links Of The Past Week Episode 14
author: SQLDenis
type: post
date: 2009-03-13T17:59:08+00:00
url: /index.php/datamgmt/datadesign/sql-friday-the-best-sql-server-links-of-15/
views:
  - 6459
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - sql friday

---
Time for another episode of the **SQL Friday, The Best SQL Server Links Of The Past Week** show.
  
Here is what I found interesting this past week in SQL Land:

**[SQL Server 2008 Auditing Resources][1]**
  
Lara Rubbelke points us to some good SQL Server 2008 Auditing Resources

**[Spatial Indexes in SP1, almost no hints required][2]**
  
Bob Beauchemin is letting us know that with SQL Server 2008 Service pack 1 Spatial Indexes require almost no hints at all

**[Summarizing previous posts about defensive database programming][3]**
  
Alexander Kuznetsov summarizes his previous posts about defensive database programming and also gives you all the links to the previous posts

**[How to use computed columns to improve query performance][4]**
  
The PSS SQL Server Engineers show you how you can use computed columns to improve query performance

**[Do you have Instant File Initialization?][5]**
  
Tibor Karaszi explains what Instant File Initialization is and why it matters

**[Is it time for SQL Server to have Isolated Transactions?][6]**
  
Arnie Rowland suggests that there is a need for a completely ‘Isolated Transaction’. That is, a transaction that can be started in the midst of a ‘regular’ transaction (parent), and that will be durable regardless of the outcome of the parent transaction. Using this ‘Isolated Transaction’, once a commit occurs, even a rollback of the parent transaction will not undo the saved work. The activity log is properly persisted and durable.

**[Granting rights to all objects in a database][7]**
  
Louis Davidson shows us a cool way to grant rights to all objects in a database



That is it for this week, I will tag the weekly posts with **SQL Friday** in case you want to see the whole archive in the future

\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][8] forum or our [Microsoft SQL Server Admin][9] forum**<ins></ins>

 [1]: http://sqlblog.com/blogs/lara_rubbelke/archive/2009/03/07/sql-server-2008-auditing-resources.aspx
 [2]: http://www.sqlskills.com/BLOGS/BOBB/post.aspx?id=27658d6b-b6dc-47d4-8fcb-9540e7b94f0c
 [3]: http://sqlblog.com/blogs/alexander_kuznetsov/archive/2009/03/08/summarizing-previous-posts-about-defensive-database-programming.aspx
 [4]: http://blogs.msdn.com/psssql/archive/2009/03/09/how-to-use-computed-columns-to-improve-query-performance.aspx
 [5]: http://sqlblog.com/blogs/tibor_karaszi/archive/2009/03/09/do-you-have-instant-file-initialization.aspx
 [6]: http://sqlblog.com/blogs/arnie_rowland/archive/2009/03/09/is-it-time-for-sql-server-to-have-isolated-transactions.aspx
 [7]: http://sqlblog.com/blogs/louis_davidson/archive/2009/03/13/granting-rights-to-all-objects-in-a-database.aspx
 [8]: http://forum.lessthandot.com/viewforum.php?f=17
 [9]: http://forum.lessthandot.com/viewforum.php?f=22