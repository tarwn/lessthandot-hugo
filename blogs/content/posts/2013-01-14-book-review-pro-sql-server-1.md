---
title: 'Book Review: Pro SQL Server 2012 Practices – Indexing Outside the Bubble'
author: Jes Borland
type: post
date: 2013-01-14T11:27:00+00:00
ID: 1914
excerpt: 'Last year, I had the opportunity to contribute to my first book, Pro SQL Server 2012 Practices. This was a huge honor, and the list of my co-authors is remarkable. What we’ve decided to do is each take one chapter, written by someone else, and post a re&hellip;'
url: /index.php/itprofessionals/book-review/book-review-pro-sql-server-1/
views:
  - 11930
rp4wp_auto_linked:
  - 1
categories:
  - Book Review

---
Last year, I had the opportunity to contribute to my first book, [Pro SQL Server 2012 Practices][1]. This was a huge honor, and the list of my co-authors is remarkable. What we’ve decided to do is each take one chapter, written by someone else, and post a review. I reviewed Jason Strate’s ([blog][2] | [twitter][3]) chapter, “Indexing Outside the Bubble”.

<img style="float: right;" src="http://www.apress.com/media/catalog/product/cache/9/image/9df78eab33525d08d6e5fb8d27136e95/A/9/A9781430247708-3d_1.png" alt="" width="277" height="350" />

(A list of all the blog reviews can be found <a href="http://www.sqlballs.com/2013/01/pro-sql-server-2012-practices.html" target="_blank">here</a>.)

When performance tuning, specifically when adding indexes, it can be very easy to get caught up in the one query or one process that you are working with. This tunnel vision can lead the best of us to forget that there is more happening within the system. There are other queries, other processes, and other users to be aware of. Jason shows us how to step outside of the bubble we are working in to focus on the full environment and the business needs.

He covers two main components in the full environment: finding missing indexes throughout the database and tuning for a specific workload. Missing indexes can be found through DMOs and the plan cache. A workload can be captured through a trace or extended events, and tuned with the Database Tuning Advisor.

Jason shows examples of each of these tools. The scripts he uses are clear and concise. He mentions the GUI tools, and shows a few examples, but focuses primarily on scripts. I like this approach (and usually use scripts myself) because, as he points out in the chapter, it is much easier to port a script from server to server than it is to fire up a GUI on each instance you want to run the task on.

When it comes to the business, Jason covers two important points. An index may not be used frequently, but can be part of a very important process. Removing it could cause a weekly or even monthly task to decrease in performance drastically. This is why it is important to know why your indexes exist. Indexing on foreign keys is also very important, because those relationships will be used frequently in queries – how often do you query data from only one table? Here, another excellent script – to find foreign keys that are not indexed – is provided.

All of these approaches are things I deal with weekly as a consultant. Indexing a database is something that can be easily overlooked, or considered a set-it-and-forget-it strategy. It should not be. A constant cycle of reviewing current index usage and adding new indexes is integral to having a well-performing database and application. This chapter gives you a set of tools to start that cycle.

This chapter is an excellent segue into the book Jason and Ted Krueger wrote about indexes – [Expert Performance Indexing for SQL Server 2012][4]. I’ve been reading this for the last couple of months and it’s proved invaluable in my day-to-day work.

Make sure you check out Pro SQL Server 2012 Practices today!

 [1]: http://www.apress.com/9781430247708
 [2]: http://www.jasonstrate.com/
 [3]: https://twitter.com/stratesql
 [4]: /index.php/ITProfessionals/book-review/book-review-expert-performance-indexing