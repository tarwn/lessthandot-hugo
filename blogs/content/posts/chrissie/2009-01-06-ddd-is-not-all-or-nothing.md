---
title: DDD is not all or nothing
author: Christiaan Baes (chrissie1)
type: post
date: 2009-01-06T05:34:22+00:00
ID: 272
url: /index.php/architect/designingsoftware/ddd-is-not-all-or-nothing/
views:
  - 2187
categories:
  - Designing Software
tags:
  - ddd
  - design

---
Today, [Jimmy Bogard][1] made a [post][2] over at [LosTechies.com][3] about DDD and it not being about patterns but all about the Domain and how you model it.

Since his words are better then mine, I would like to quote him.

> One question Rob Conery asked me during a conversation on DDD was, “How do I recognize an application built with DDD?” We’ve already noted that you can’t look towards a set of patterns, so how do we recognize one? A DDD application is one whose design is driven by the domain, hence “Domain-Driven Design”.

I think he has a point. Domain driven design should make the domain the first and most important aspect of the application. I think it&#8217;s also the most natural way to implement OOP. 

> Another tactic to recognizing DDD is have both the domain experts and team explain the domain. Counting the number of clarifications and special cases, “well, except, but” and “we” versus “they” provides a good indication that the domain experts and team are not on the same page.

Of course, it will never be perfect, since the domain experts sometimes forget to tell certain things to the team. But then, at the end of the process, the team should be domain experts too, to a point at least. They may lack the experience but they should know most of the process, if they don&#8217;t, then they haven&#8217;t understood the process. So, if you model a milk route for a milkman, you don&#8217;t have to go around taking out the milk for 5 weeks in a row, but you should have at least been there once. 

REMEMBER, you can&#8217;t always trust the domain experts to tell you everything they know. They sometimes take things for granted that aren&#8217;t as obvious to a third party. 

> DDD is an architectural style that encourages model-driven design and a domain-driven model. Following this style is following DDD, and merely implementing patterns only indicates that we can implement patterns.

That summed it up quite nicely for me.

I would also like to add that DDD is not about what library you use and the domain is also OO-language agnostic. The implementation might vary a little from language to language but it should turn out to be the same in the end.

 [1]: http://www.lostechies.com/blogs/jimmy_bogard/default.aspx
 [2]: http://www.lostechies.com/blogs/jimmy_bogard/archive/2009/01/05/ddd-is-not-all-or-nothing.aspx
 [3]: http://www.lostechies.com/