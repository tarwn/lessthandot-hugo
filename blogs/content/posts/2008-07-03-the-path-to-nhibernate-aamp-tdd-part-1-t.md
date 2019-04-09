---
title: My Path to the Dark Side part 1 – The Beginning
author: Alex Ullrich
type: post
date: 2008-07-03T09:30:14+00:00
ID: 61
url: /index.php/desktopdev/mstech/the-path-to-nhibernate-aamp-tdd-part-1-t/
views:
  - 6597
rp4wp_auto_linked:
  - 1
categories:
  - 'C#'
  - Microsoft Technologies
tags:
  - 'c#'
  - nhibernate
  - unit testing

---
For a while now, I've been brewing my own beer. Being a programmer, when I'm brewing of course I also need a way to store my recipes, and how they turn out. So I have a simple application I wrote that most recently uses SQL Server Compact edition for its database. 

Recently I was trying to make some improvements to my domain in this application (namely, separating the “Impression” information from the recipe itself so that I could allow my special lady, and anyone else who drinks one of my beers, weigh in with their impressions of it). But I found myself getting incredibly bogged down in all the ad-hoc SQL that the program must maintain (SQL Server Compact doesn't support stored procedures). Because I am used to using stored procs for everything, I'm also used to having a very simple and clean DAL. The necessity to create queries to insert/update each object type that I was trying to persist was a new one to me, and I found it most distasteful (why should I need 45 lines of code to update my recipe?). It makes for a lot of unnecessary complexity in my application code.

With stored procs, I think this is acceptable. Just define the proc name, assign your parameters, and run it. This also gives you the benefit of being able to limit changes to the database only in many situations. However with SQL Server Compact, it really blows. I need to make my changes to the database, then I need to go through all my application code and recreate queries there. Then I have to test it. Not fun! I found I was spending much more time modifying and testing the data access code to to deal with the redesigned objects than I was modifying the objects themselves. For a database that was doing so little actual work, this is unacceptable to me.

Instead of trying to modify this application, I decided I will start from scratch, and use what I have learned from the first time around to create a better and more maintainable application. With this decision made, the question becomes “How can I do this?”. I've been learning a lot about test-driven development in school, so this will give me a chance to apply that knowledge, using nUnit. But will applying TDD really help me to clean up this data access crap? I don't think so. I suppose I could write my own class to generate the updates, etc…, but I do enough work at work. So I need a way to do this more easily.

Luckily my esteemed colleague Chrissie1 has been preaching the gospel of nHibernate to me for a long time now. I've never really bought in because most of my applications at work rely heavily on the database to do actual work. But for this application, I really only need to use the database to persist my objects. Maybe a tool like nHibernate could really help me here?

I've started down the road, and I think that it really will. This series of blogposts will contain my ramblings as I travel down the path of recreating this application. We'll start with development of the domain model. The program has a very simple domain model (Recipe, Ingredient, Impression, and Person) which will make it a good way to demonstrate new concepts without getting bogged down in complex application design. Here is a diagram of the application's problem domain:

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev//RecipeTrackerDomain.jpg" alt="" title="" width="709" height="530" />
</div>

The goals of this exercise will be:

  * Eliminate or minimize application's dependency on the database.
  * Allow the application to easily work with another database.
  * Produce more reliable code by using Test Driven Development methodology
  * Minimize the effort required to alter the way the application manages and persists domain objects

Once I'm done with this, I still have the actual program to write, but there will be some follow up posts discussing how much easier (it had better be) to develop the UI. Hopefully this series of posts will leave the reader (and me!) with a good understanding of the basics of nHibernate and nUnit.