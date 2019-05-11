---
title: Some Thoughts on Session Management
author: Alex Ullrich
type: post
date: 2010-08-16T21:31:00+00:00
ID: 870
url: /index.php/webdev/serverprogramming/aspnet/some-thoughts-on-session-management/
views:
  - 4580
rp4wp_auto_linked:
  - 1
categories:
  - ASP.NET
tags:
  - asp.net
  - 'c#'
  - nhibernate

---
The last time I did a post on NHibernate (or any post for that matter – I guess I've been a bit busy) [Ben][1] asked a question about what I ended up using for session management in the application I've been working on. I guess I could come out and answer it, but I'd hardly get a new post out of that. Instead I will break out the tried and true answer for all things IT, "It depends". Let's take a look at what it depends on.

## What is the Session?

This is probably the best place to start. If you're used to working with ADO.net, jdbc, or something similar, it's easy to think of the session as nothing more than a window into your database that allows you to execute queries, like an [IDbConnection][2], but it does a lot more for you if you let it. NHibernate sessions provide a great [Unit of Work][3] container, allowing you a great deal of control over how things get written to your database. Unless you are using a stateless session (a session configured not to hold anything in memory) the session will also use NHibernate's first level cache to store objects that are in use, helping you to avoid excessive trips to the database.

## What is your Unit of Work?

This is the $64,000 question. Once you know what your unit of work is, session management will more or less solve itself. As you can imagine, this is highly dependent on the nature of the application you're working on. In most cases the unit of work is synonymous with a business transaction. It represents the minimum amount of work that you want to commit to your underlying storage mechanism. This is an all or nothing proposition – if your unit of work requires you to write four objects to a data store, and only three succeed, then the three that succeeded will be cancelled, or rolled back. 

Keeping this in mind, a desktop application using an embedded database for persistence may be able to get by with a single session for the life of the application, as long as transactions are properly committed and the session is flushed when the app shuts down. This gives you the benefit of storing a LOT of data in the first level cache, which can be very beneficial when you're positive no one else will be modifying it (though, with an in memory database you may not even need the first level cache 😉 ). 

At the other end of the spectrum you could find SOA type applications where the unit of work is performed across several round trips between client and server. Using this strategy, subsequent requests to the server need to be able to find their way back to the same session that their work was started with, otherwise they won't have the data they need. In most cases I'd imagine this requires a more complete unit of work implementation that can be persisted across requests and use the NHibernate session under the hood. 

## What about Me?

Like most people, I find myself somewhere in the middle of this spectrum. My application is web-based, and the primary unit of work can be completed in a single request, so I ended up using a session per request strategy. I found that [StructureMap][4] makes managing this a lot easier. I need to first register a Session Factory (as a singleton) with StructureMap. I then tell StructureMap how to retrieve an ISession from this factory (using the ConstructedBy method), and also ensure that it caches sessions _per HTTP context_. This is all registered from the application layer, but consumed farther down in the persistence layer. Finally, in Application_EndRequest I call a function to clean up anything that StructureMap has cached by HTTP context. 

I mentioned that this was the "primary" unit of work, there is one area that this does not cover, and that is authentication. For a while I used built in providers (for SQL Server, then the MySQL provider in MySQL.Data), but when I moved databases again (to postgres) I decided it was time to make a change. Now I'm using a [MembershipProvider][5] based on NHibernate, to ensure that it is as easy to move my authentication mechanism across database platforms as it is to move the domain logic. To me, using the session per request strategy didn't really make a lot of sense here because the provider isn't really tied to the HTTP context, but to the application itself. In addition, all data used by the authentication process can ONLY be changed through the membership provider. So, for authentication I keep a single session open for the entire life of the application. This would present some interesting challenges if I were to need to scale across multiple servers, but the code changes to the provider will not be too difficult. In the meantime, I see some pretty good performance benefits from using the single session, as the provider rarely needs to hit the database, or even the second level cache (memcached).

In summary, I find it difficult to offer advice about session management without knowing more about the application. Others have already explained the underlying concepts far better than I could ever hope to, so in a way writing this post feels like a complete waste. But I like writing about my thought process when it comes to matters like these, so I did enjoy writing it. And I think there is some value in sharing this thought process with others, so hopefully at least a few people get something out of reading it. Especially Ben, if he's still reading.

 [1]: /index.php/DesktopDev/MSTech/two-years-with-nhibernate-lessons-learne#c3958
 [2]: http://msdn.microsoft.com/en-us/library/system.data.idbconnection.aspx
 [3]: http://martinfowler.com/eaaCatalog/unitOfWork.html
 [4]: http://structuremap.github.com/structuremap/index.html
 [5]: http://msdn.microsoft.com/en-us/library/system.web.security.membershipprovider.aspx