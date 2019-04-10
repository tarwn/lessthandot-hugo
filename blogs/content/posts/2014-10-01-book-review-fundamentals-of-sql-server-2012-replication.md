---
title: 'Book Review: Fundamentals of SQL Server 2012 Replication'
author: Jes Borland
type: post
date: 2014-10-01T15:16:38+00:00
ID: 2998
url: /index.php/uncategorized/book-review-fundamentals-of-sql-server-2012-replication/
views:
  - 6351
rp4wp_auto_linked:
  - 1
categories:
  - Uncategorized

---
[<img class="alignleft wp-image-2999 size-full" src="/wp-content/uploads/2014/10/1843-replication.png" alt="1843-replication" width="117" height="135" />][1]I've been working with transactional replication in SQL Server a lot this year. A lot. I understood the basics of how it worked, but I wanted to know more – particularly about security, how log readers work, monitoring, and troubleshooting. So, I downloaded a free e-book from <a href="https://www.simple-talk.com/" target="_blank">Simple Talk</a>, <a href="https://www.simple-talk.com/books/sql-books/fundamentals-of-sql-server-2012-replication-by-sebastian-meine/" target="_blank">Fundamentals of SQL Server 2012 Replication</a> by Sebastian Meine. [
  
][1] 

The description says the book “will walk you through setting up different replication scenarios. All hands-on exercises are designed with security best practices in mind.”

The first chapter is a solid introduction to replication components and naming. There are a lot of moving pieces to replication – publishers, distributors, subscribers, publications, articles, subscriptions, and more. This chapter was a  good overview of these basics.

The second chapter is a walk-through of a basic transactional replication setup. However, it uses two virtual machines that reside on the same computer. I didn't feel this was in any way an accurate representation of what people will deal with in the real-world. I wish the author had spent the time to set up a full-on lab with multiple severs.

The next four chapters cover individual parts more in-depth: the distributor, the agents, the publication, and the subscription. I learned a lot about the agents in particular, especially the log reader agent, which is a central component of replication.

Chapter 8 covers the SQL Server Agent jobs that are used for transaction replication. I found this helpful when trying to sort through everything I find on systems using it.

Chapters 9 through 12 focus on merge replication. I know this is a beast to set up and maintain. I was hoping for a lot of in-depth information on this, but I felt it was shallow. There are lots of screenshots on how to set up things like publications, subscriptions, and tracking. I was hoping for a lot more information on conflict resolution, however. There are a few pages about the default and how to set up custom resolvers, but not nearly as much as I was hoping for.

Then, there is only one chapter on monitoring. One. I think a book could be written on this topic, especially since I know there are many pitfalls to using the Replication Monitor. There is also only one chapter on troubleshooting, and that left me wanting a lot more.

With that said, I do have many notes I highlighted throughout the book. (Thank goodness for the Kindle app letting me do this!) For example, the Distribution Agent can live on the distributor or the subscriber, depending on if it's a push or pull model. You can replicate stored procedures along with tables and indexes – and it can be the definition, or the definition and each execution. Merge replication uses a lot of triggers. Replication Monitor isn't part of SSMS, it's a separate executable.

I wanted to learn more about monitoring and troubleshooting, but I don't feel I gained much more knowledge than I had in my hands-on experience. I wanted to learn more about merge replication, too, but that wasn't covered to the depth I wanted – it could use its own book.

I would recommend this book to someone who's never used transactional replication and needs to understand its moving parts, though. It's good on the basics, but I'll stress that using replication is a hands-on activity. You need to test, test, test, and test some more – no book can convey all the nuances of this particular technology!

 [1]: https://www.simple-talk.com/books/sql-books/fundamentals-of-sql-server-2012-replication-by-sebastian-meine/