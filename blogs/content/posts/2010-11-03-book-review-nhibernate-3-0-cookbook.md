---
title: Book Review – NHibernate 3.0 Cookbook
author: Alex Ullrich
type: post
date: 2010-11-03T14:21:21+00:00
url: /index.php/desktopdev/mstech/book-review-nhibernate-3-0-cookbook/
views:
  - 6680
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft Technologies
tags:
  - book
  - 'c#'
  - nhibernate

---
I just finished reading the [NHibernate 3.0 Cookbook][1], part of Packt Publishing&#8217;s Open Source series, and I&#8217;ve been really impressed. The book is written by [Jason Dentler][2], whose blog I&#8217;m sure anyone doing frequent searches for NHibernate has run across once or twice in their travels.

What really stood out to me about this book was how easy it was to read. You can tell that Jason went out of his way to make the book approachable for people unfamiliar with the concepts (without dumbing down the content) and in my opinion it really paid off. It makes for a much lighter and more enjoyable read than most technical books (I could routinely knock out 40-50 pages in a 45 minute train ride). I think it also makes it easier to grasp the concepts as well (at least this was true for the topics I was less familiar with). 

The book is broken up into chapters representing the cornerstones of NHibernate, each chapter containing a handful or two of recipes illustrating solutions to commonly encountered problems. The chapters are broken out as follows:

  * Models And Mappings
  * Configuration And Schema
  * Sessions And Transactions
  * Queries
  * Testing
  * Data Access Layer
  * Extending NHibernate
  * NHibernate Contribution Projects

I view most of these as **essential** knowledge for people developing with NHibernate, and I have trouble thinking of an essential topic that is left off the list. The book is intended as a quick reference containing solutions to common problems, but because of the way it is written and the breadth of the content it could serve as a good introduction for a newcomer as well. It would be impossible to cover **everything** about NHibernate in a book twice as long as this one, but I think there is enough here to leave the reader in a good place to be able to solve any other problems they run into (with some help from the internets of course).

I learned a few new things over the course of reading the book. The first was in the recipe about configuring NHibernate from code (in the Configuration and Schema chapter). It discusses the NHibernate.Cfg.Loquacious namespace, which offers a much better way to configure NHibernate in code than the normal configuration namespace. I typically use Fluent NHibernate lately, so I may never have come across this had I not read the book. I&#8217;m certainly glad that I know it&#8217;s there now. Other things the book turned me on to included [ConfORM][3], a very cool take on automapping and [NHibernate.Spatial][4], both of which I will need to give a try at some point in the not too distant future.

There were a lot of things to like about the book, but I&#8217;d have to say my favorite was the chapter dedicated to the various contribution projects. Some of these libraries really help NHibernate stand out from other ORMs, but they aren&#8217;t always that well documented (I guess this is a problem with OSS in general). They address many common needs, but because of the lack of documentation (or maybe because people don&#8217;t always know where to look) I&#8217;ll sometimes encounter people trying to recreate something (like caching) that the contribution projects have quite well covered. To see a book like this spreading the word about these libraries makes me smile. In addition to making me smile, this chapter offers a great intro to NHibernate.Caches, and probably the best example of setting up NHibernate.Search with Lucene.net that I&#8217;ve come across.

I also like that it uses a consistent domain model throughout the book. You could certainly get the same experience by scouring the interwebs and working through examples, but I suspect some of the lessons would be lost to the tedious act of setting up the domain objects used in each example. Keeping the model used consistent allows the reader to really focus on the core subject of each recipe.

I think this book would be a good read for experienced NHibernate users trying to get up to speed on version 3 quickly, or for anyone looking for a quick reference that cuts right to the heart of common problems. I&#8217;m not sure about how brand new NHibernate users would like it, but I have a feeling that it would work well for the type of users that learn best by doing.

 [1]: https://www.packtpub.com/nhibernate-3-0-cookbook/book
 [2]: http://jasondentler.com/blog/
 [3]: http://code.google.com/p/codeconform/
 [4]: http://nhibernatespatial.codeplex.com/