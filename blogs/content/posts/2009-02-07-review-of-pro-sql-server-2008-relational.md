---
title: Review of Pro SQL Server 2008 Relational Database Design and Implementation
author: SQLDenis
type: post
date: 2009-02-07T17:45:30+00:00
ID: 318
url: /index.php/webdev/webdesigngraphicsstyling/review-of-pro-sql-server-2008-relational/
views:
  - 8627
rp4wp_auto_linked:
  - 1
categories:
  - Web Design, Graphics and Styling
tags:
  - book
  - database design
  - review
  - sql server 2008

---
Good database design is extremely important if you want to have a database that performs well and is easily modifiable.
  
How many times did you have to go back and change countless lines of code because your database was not normalized and it was not easy to add another payment type to the existing database?

This is where [Pro SQL Server 2008 Relational Database Design and Implementation][1] comes in; in the first couple of chapters you are taught how to design a database in a normal form. Most books that deal with database design are dry and boring; you need to read a chapter several times to digest the information leaving you with a headache. Not this book, [Pro SQL Server 2008 Relational Database Design and Implementation][1] makes this almost fun, the author goes through the phases of database design in a fun way which is understandable to the common man

After the design is done and you have your database you will be shown how you can protect the integrity of your data. Remember nothing in the database is as important as to make sure the data is correct, if you have bad data then you might as well have no data at all. This book will show you how to protect the integrity of your data by using constraints and triggers

There is a whole chapter of some good tips and tricks, patterns and query techniques. I think that you will find out you can solve a whole lot of business problems with a numbers table much faster than if you had to do it without

Security is a big topic these days, look on the internet, ever week you here horror stories how some data was stolen or otherwise compromised. Fear not there one whole chapter about securing access to your valuable data

Table structure and indexing is also covered, how are tables stored internally, the differences between clustered and non clustered indexes. These are very important concepts to understand; there are plenty of stories of people who designed a database and it worked flawless when they tested it. Once they loaded it up with real data in the real world it became painfully slow, by then they had to spend countless hours trying to refactor that mess. This book will prevent these kind of things

Coding for concurrency is also covered. This is also a very important concept. Explained are the different isolation levels, the difference between optimistic and pessimistic locking and best practices

Each chapter has a summary and a best practices chapter. This is very handy because you can quickly get an overview of the whole chapter in a page or two.

I highly recommend this book to anyone who wants to learn or get better at designing databases. Having a bad database design is like building a house on a weak foundation; sooner or later you will pay for it and you will have to tear parts down to fix that faulty foundation.

When it comes to database design and SQL Server I have no doubt in my mind that this is the book to get. This is the third version of the book I have read and I am pleased to see that every edition is a little better than the previous one.

Download chapter 1 here: http://www.apress.com/book/downloadfile/4090 to get a &#8216;feel' for the book

Amazon link here: [Pro SQL Server 2008 Relational Database Design and Implementation][1]

If you are interested in some more info about the author then check out the interview I did a while back: [Interview With Louis Davidson Author of Pro SQL Server 2008 Relational Database Design and Implementation][2]

 [1]: http://www.amazon.com/gp/product/143020866X/002-3562882-4309664?ie=UTF8&tag=sql08-20&linkCode=xm2&camp=1789&creativeASIN=143020866X
 [2]: /index.php/DataMgmt/DataDesign/interview-with-louis-davidson-author-of-