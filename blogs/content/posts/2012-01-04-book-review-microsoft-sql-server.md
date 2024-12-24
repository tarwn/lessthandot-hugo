---
title: 'Book Review: Microsoft SQL Server 2008 Internals'
author: Jes Borland
type: post
date: 2012-01-04T12:28:00+00:00
ID: 1474
excerpt: "At SQL Saturday #67, Chicago 2011, I won the SQLskills swag bag. Included was the book Microsoft SQL Server 2008 Internals. I've finally finished it, cover-to-cover! Here's my review."
url: /index.php/datamgmt/dbprogramming/book-review-microsoft-sql-server/
views:
  - 16819
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
Remember this moment? 

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/IMG_1122.JPG?mtime=1325477156"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/IMG_1122.JPG?mtime=1325477156" width="484" height="648" /></a>
</div>

I sure do. At SQL Saturday #67, Chicago 2011, I won the SQLskills swag bag. My second-favorite part of the package was the book, [Microsoft SQL Server 2008 Internals][1]. It was written by Kalen Delaney ([blog][2] | [twitter][3]), Paul Randal ([blog][4] | [twitter][5]), Kimberly Tripp ([blog][6] | [twitter][7]), Conor Cunningham ([blog][8]), Adam Machanic ([blog][9] | [twitter][10]), and Ben Nevarez ([blog][11] | [twitter][12]). (My favorite part was the SQLskills Sharpie pen!) This is **The Book** to read if you want to know the "how" of SQL Server. 

**Rating:** 4.5/5 

**The Bad** 

I'm not a computer science major. I'm not a mathematics major. Some of the topics covered were in such depth that I was in over my head – for example, the tables chapter went into byte-swapping to read pages. I could follow along, but I won't pretend that I understood all of it, that I really _got_ it.

Some of the topics seemed a bit esoteric. 

There are a lot of sections where queries are run, but the results aren't displayed. I imagine you are supposed to be following along by running the code. However, my reading time starts at 5:30 am with a cup of coffee, and I don't have my laptop turned on to follow along. (This is a pet peeve of mine with blogs, too. If you ran the query, please show me your results. I hope mine are similar.) 

**The Good** 

Everything else. This book is amazing. I know more about SQL Server than I thought I ever would. 

Even the tough concepts are written in a way that anyone can understand. 

**Chapter 1 – SQL Server 2008 Architecture and Configuration** 

The first chapter talks about what makes SQL Server run – SQLOS, memory, resource governor, configuration manager, trace flags, and more. This chapter is a good overview. It's also helpful for anyone familiar with earlier versions of SQL Server, as it talks about how system tables have been replaced with catalog views. Immediately-useful tips are presented throughout the chapter, like setting min and max server memory, and finding where the default trace is located. 

**Chapter 2 – Change Tracking, Tracing, and Extended Events** 

It's incredibly useful to know what is happening in your databases, why, and who started it. (I bet parents would like this power.) 

Running Profiler and traces is something all DBAs should be familiar with. I enjoyed the section on server-side traces, and learning more about what the individual parts of the scripted trace will do. The future of tracing, though, lies in Extended Events, which are covered quite in depth. Adam gives a good overview of packages, events, types, predicates, actions, and targets. However, with XE, there's no substitute for actually working with them. (I highly recommend Jonathan Kehayias's [XEvent a Day blog series][13].) 

**Chapter 3 – Databases and Database Files** 

Many aspects of data files are covered here – creating, deleting, shrinking, and filegroups. The options available for a database (like user mode, cursor options, and auto options) are discussed. The tempdb database is also talked about here. I knew there were a lot of things stored here, but it was interesting to learn how it's optimized. 

**Chapter 4 – Logging and Recovery** 

Here, you learn about the transaction log, and what is written to it. One of the best things about this chapter was the discussion about virtual log files – how they're created, how you view them, and how to manage them. The different recovery models – FULL, BULK_LOGGED, and SIMPLE – are covered here, too. 

**Chapter 5 – Tables** 

There are 86 pages dedicated to the discussion of tables. This chapter was the second-hardest for me to get through. It went into incredible depth, but I don't know if I'll need to open and read an individual data page. 

I had no idea there was so much behind tables, the basic building block of a database. First, data types are discussed. I found this section fascinating, particularly when learning how date and time values are stored. The data pages section was intimidating. Here, you will see pages broken down into their basic parts. Then, the structure of each row is decomposed. To wrap up the chapter, there is a discussion of how heaps are modified. It shows how it handles insertions, updates, and deletions. 

**Chapter 6 – Indexes: Internals and Management** 

The introduction to this chapter says that while there is no magic bullet to make a database run faster, indexes are the closest thing. I couldn't agree more, especially after this in-depth discussion. The different tools for analyzing indexes are listed – dm\_db\_index\_physical\_stats and DBCC IND. Index structure is covered. This section is really well-done, with good examples of queries and their results, and diagrams to understand how indexes are traversed. I feel much more confident about creating indexes, and _why_ I'm doing it, after reading this. 

Special indexes are discussed, too – computed columns, indexed views, full-text, spatial, and XML. Then, you learn what happens to an index when data is added or deleted. It's good to know how SQL Server will handle the changes in data and structures. 

The chapter wraps up with how to manage indexes. The ALTER INDEX command has a few options, and they can greatly affect how this command works. Learning how online index builds work was really cool. 

**Chapter 7 – Special Storage** 

Not all database objects will fit on one 8KB page of database. What happens then? It can be stored as LOB (Large Object) data. This is more viewing of pages and hex values, and again, was a little over my head. 

Filestream data is covered next. This is an intriguing feature of SQL Server that I want to use at some point. The chapter covers enabling it, inserting data, updating data, logging changes, and garbage collection. The nice thing here is that it shows how things change in both the database and the file system. 

Then I learned about sparse columns. This was a really useful section for me. I'd heard of them, but didn't understand them – now I do. There are a few rules for them, and they are only useful in certain situations. But seeing how they are stored physically, and the space savings that can be gained, is really interesting. 

New to SQL Server 2008 is data compression, and it is covered nicely here. Row compression is talked about at length, including an explanation of the column descriptor format, and what each section and bit means. Page compression is also covered. I get this now, which I hadn't before, and I'm really happy about that. It's explained in a really easy-to-understand way. 

The chapter wraps up with table and index partitioning. I've read blogs about partitioning. I studied it when I was working towards certification. But even with this, I still couldn't open up an SSMS window and build a partition. 

That chapter is packed full of in-depth but really useful information. 

**Chapter 8 – The Query Optimizer** 

If you want to know, in-depth, how SQL Server determines how it is going to get the results of your query, this is the chapter for you. Operators are discussed, as are trivial plans and the Memo. The statistics section was very helpful. 

There are lots of seemingly-random sections here. Two paragraphs about Halloween protection. Information on wide update plans. Sparse column updates. Partition-level lock escalation. 

I thought the most useful part was at the end, where different query and table hints that affect the optimizer are discussed, like FORCESEEK and FAST. 

**Chapter 9 – Plan Caching and Recompilation** 

This is a subject Kalen knows a lot about. She does a great job of explaining why plans are cached, and why SQL Server picks one plan over another. I think the most useful section here is an overview of the metadata. Several DMVs are broken down, such as sys.dm\_exec\_sql\_text and sys.dm\_exec\_cached\_plans. Because many of these have similar names, it can be hard to tell them apart. 

There is also excellent information on troubleshooting plan cache issues. I've run into these on more than one occasion as a DBA. It's great to be able to see what the cause of the problem is, and resolve it. 

**Chapter 10 – Transactions and Concurrency** 

This was the most useful chapter for me. I learned more here than anywhere else. I think everyone who works with SQL Server – everyone – should read this. One thing done differently here is that potential problems are covered first – it's good to see what could happen, and then see how it could happen. These are things like lost updates and dirty reads. Then, the five isolation levels are discussed. 

Once you know the basics, then different types of locks are covered. The granularity of locks is covered. You see how to view locks with DMVs. There's a great diagram about lock compatibility. Page and row locking are compared. There's a section about table and query hints for locking, also. The information here is invaluable. 

**Chapter 11 – DBCC Internals** 

I was always a good DBA, running DBCC CHECKDB on my databases regularly. I've only seen it fail with corruption once, and was able to recover from a backup, which, as a good DBA, I was also taking regularly. But what if you get error 8935? Or error 8978? Will you know why, and what to do about it? With this chapter, you could.
  
Knowing the steps the database takes during a check is very in-depth, and very technical, but interesting to read about. Knowing what the repair options are, and what they do, is very helpful. I even (finally) understand what the DATA_PURITY option really does. 

**My Brain Hasn't Exploded, But It Was Close A Couple Times** 

This book was really in-depth and really technical. I feel much smarter after reading it. I have a feeling that in three months or a year, I'll have a problem, and I'll think, "Oh, I read about that somewhere." I'll be able to pull this out and reference it. So, I may not understand all of it right now, but in the long run, it was a great decision to stick with it and read it. 

Thank you Kalen, Paul, Kimberly, Conor, Adam, and Ben, for sharing all of this knowledge!

 [1]: http://www.microsoft.com/learning/en/us/book.aspx?ID=12967&locale=en-us
 [2]: http://sqlblog.com/blogs/kalen_delaney/
 [3]: https://twitter.com/#!/sqlqueen
 [4]: http://www.sqlskills.com/blogs/paul/
 [5]: https://twitter.com/#!/PaulRandal
 [6]: http://www.sqlskills.com/blogs/kimberly/
 [7]: https://twitter.com/#!/KimberlyLTripp
 [8]: http://blogs.msdn.com/b/conor_cunningham_msft/
 [9]: http://sqlblog.com/blogs/adam_machanic/
 [10]: https://twitter.com/#!/AdamMachanic
 [11]: http://www.benjaminnevarez.com/
 [12]: https://twitter.com/#!/BenjaminNevarez
 [13]: http://sqlblog.com/blogs/jonathan_kehayias/archive/tags/XEvent+A+Day/default.aspx