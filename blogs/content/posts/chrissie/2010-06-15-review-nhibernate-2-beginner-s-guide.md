---
title: 'Review: NHibernate 2 – Beginner’s Guide'
author: Christiaan Baes (chrissie1)
type: post
date: 2010-06-15T08:31:40+00:00
ID: 820
url: /index.php/desktopdev/mstech/review-nhibernate-2-beginner-s-guide/
views:
  - 6322
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - nhibernate
  - review

---
The people at [Packt publishing][1] asked me to review [NHibernate 2: Beginner&#8217;s Guide][2] by [Aaron B. Cure][3].

![Bookcover][4]

This book is for anyone who uses NHibernate. 

> Who this book is written for
> 
> This book is for new and seasoned developers of .NET web or desktop applications who want a better way to access database data. It is a basic introduction to NHibernate, with enough information to get a solid foundation in using NHibernate. Some advanced concepts are presented where appropriate to enhance functionality or in situations where they are commonly used.

I have been using NHibernate for several (3 or 4) years now (man I sound old). And I like it a lot. In all these years, I have learned when to use it and when to leave it alone. These are things you will only learn by using it. This book won&#8217;t teach you this and it doesn&#8217;t pretend to want to do this. This book will also not teach you how to do Object Oriented (OO) programming. You should know OO before beginning with this and NHibernate in general. If you don&#8217;t do OO then an [ORM (Object Relational Mapper)][5] will sound weird and useless to you. If you don&#8217;t understand why VB.Net and C# programmers should do OO, you will have no need for this book. 

It does show you how to solve the OO and relational incompatibility. It shows you how to do the mapping with XML and with [Fluent NHibernate][6]. It also shows you how to use a session and what a [unit-of-work][7] pattern is and when and why you should use that. 

It shows you how to set the logger and how to use this to debug your code. It also shows you how to write queries and filters. 

And at the end, it shows you how to use code generation to make writing code go faster with NHibernate. 

And the best bit was that all code examples were in VB.Net and C#, but it got a bit boring to see &#8220;and here we have the same in VB.Net&#8221;. Clearly the author does not love VB.Net like some of us do. And he doesn&#8217;t need to, he just needs to hide it better. 

So what do I think of this book? Although I have used NHibernate for a while now, you can always learn more. I learned a few little things and just for that, it was worth it. I learned that code generation is not for me (not in my situation anyway). I think this book is a good book for the beginning NHibernate user, both C# and VB.Net; you will learn a lot from it and you will have less frustrations than me to begin with. However, it is assumed that you know OO programming and that you know when to use it and when not. I can recommend it for seasoned NHibernate users as a quick read, you will perhaps not learn a lot from it &#8211; &#8220;the breadth of information presented may shed light on features of NHibernate that you&#8217;ve never had the opportunity to use before.&#8221; I learned what NHibernate Burrow was and what to use it for after having never looked into it before. And when and why to use MaxRequestLength.

 [1]: http://www.packtpub.com/
 [2]: http://www.packtpub.com/nhibernate-2-x-beginners-guide/book?utm_source=blogs.lessthandot.com&utm_medium=bookrev&utm_content=blog&utm_campaign=mdb_003492
 [3]: https://www.packtpub.com/authors/profiles/aaron-cure
 [4]: https://www.packtpub.com/sites/default/files/imagecache/productview/8907OS_MockupCover_Beginers%20guide.jpg "Bookcover NHibernate 2"
 [5]: http://en.wikipedia.org/wiki/Object-relational_mapping
 [6]: http://fluentnhibernate.org/
 [7]: http://martinfowler.com/eaaCatalog/unitOfWork.html