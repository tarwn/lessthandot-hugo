---
title: 'SQL Advent 2012 Day 7: Lack of constraints'
author: SQLDenis
type: post
date: 2012-12-07T13:20:00+00:00
excerpt: |
  This is day seven of the SQL Advent 2012 series of blog posts. Today we are going to look at constraints
  
  
    
  SQL Server supports the following types of constraints: NOT NULL, CHECK, UNIQUE, PRIMARY KEY and FOREIGN KEY. Using constraints is preferred to using DML Triggers, rules, and defaults. The query optimizer will also use constraint definitions to build high-performance query execution plans.
url: /index.php/datamgmt/dbprogramming/lack-of-constraints/
views:
  - 18607
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - constraints
  - data integrity
  - sql advent 2012
  - sql server 2008
  - sql server 2008 r2
  - sql server 2012
  - ssms

---
This is day seven of the [SQL Advent 2012 series][1] of blog posts. Today we are going to look at constraints

[<img src="http://farm2.staticflickr.com/1241/4727475559_881dab57f3_m.jpg" width="240" height="240" alt="Constraints / Sacrifices" />][2]

SQL Server supports the following types of constraints: NOT NULL, CHECK, UNIQUE, PRIMARY KEY and FOREIGN KEY. Using constraints is preferred to using DML Triggers, rules, and defaults. The query optimizer will also use constraint definitions to build high-performance query execution plans.

When I interview people, I always ask how you can make sure only values between 0 and 9 are allowed in an integer column. I get a range of different answers to this question, here are some of them:

  * Convert to char(1) and make sure it is numeric
  * Write logic in the application that will check for this
  * Use a trigger
  * Create a primary key table with only the values from 0 till 9 then make this column a foreign key in the table you want to check for this

Only 25% of the people will tell you to use something that you can use from within SQL, and only 10% will actually know that this something is called a check constraint, the other ones know that there is something where you can specify some values to be used. 

## Why do we need constraints at all?

So why do we need constraints? To answer that question, first you have to answer another question: how important is it that the data in your database is correct? I would say that that is most important, after all you can have all the data in the world but if it is wrong it is useless or it might even ending up costing you money. To make sure that you don&#8217;t get invalid data, you use constraints. Constraints work at the database level, it doesn&#8217;t matter if you do the data checking from the app or web front-end, there could be someone modifying the data from SSMS. If you are importing files, constraints will prevent invalid data from making it into the tables.

Constraints don&#8217;t just have to have a range, constraints can handle complex validations. You can have regular expressions in check constraints as well, check out [SQL Server does support regular expressions in check constraints, triggers are not always needed][3] for some examples

## Constraints are faster than triggers

The reason that check constraints are preferable over triggers is that they are not as expensive as triggers, you also don&#8217;t need an update and an insert trigger, one constraint is enough to handle both updates and inserts.

## Constraints are making it hard for us to keep our database scripts from blowing up

This is a common complaint, when you script out the databases and the primary and foreign key tables are not in the correct order you will get errors. Luckily the tools these days are much better than they were 10 years ago. If you do it by hand just make sure that it is all in the correct order. Another complaint is that constraints are wasting developers time, they can&#8217;t just populate the tables at random but have to go in the correct order as well. 

## Some examples of constraints

Since I like the DRY principle, I won&#8217;t just copy and paste some examples that lready exist in a post I wrote a while back, take a look at this post [Dammit people, get to know check constraints and use them!][4] to see some examples of check constraints

That is all for day seven of the [SQL Advent 2012 series][1], come back tomorrow for the next one, you can also check out all the posts from last year here: [SQL Advent 2011 Recap][5]

 [1]: /index.php/DataMgmt/DBProgramming/sql-advent-2012-here-is
 [2]: http://www.flickr.com/photos/bjornmeansbear/4727475559/ "Constraints / Sacrifices by bjornmeansbear, on Flickr"
 [3]: /index.php/DataMgmt/DBProgramming/sql-server-does-support-regular-expressi
 [4]: /index.php/DataMgmt/DBProgramming/MSSQLServer/dammit-people-get-to-know
 [5]: /index.php/DataMgmt/DataDesign/sql-advent-2011-recap