---
title: Latest article by SQL Server MVP Erland Sommarskog is a gem
author: SQLDenis
type: post
date: 2011-02-21T22:22:00+00:00
ID: 1048
excerpt: |
  SQL Server MVP Erland Sommarskog has posted his latest article yesterday and I highly recommend printing it out/transferring it to your ebook reader and reading it.
  Of course I think most of you are already familiar with Erland's article The curse and&hellip;
url: /index.php/datamgmt/datadesign/latest-article-by-sql-server/
views:
  - 5677
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - how to
  - performance
  - sql server 2005
  - sql server 2008
  - ssms
  - tip

---
SQL Server MVP Erland Sommarskog has posted his latest article yesterday and I highly recommend printing it out/transferring it to your ebook reader and reading it.
  
Of course I think most of you are already familiar with Erland&#8217;s article [The curse and blessings of dynamic SQL][1], this article I am sure will be linked in answers as much as the dynamic SQL one

Remember all those question you get where a query is fast in SSMS but slow from ADO.NET? Yep that pesky ARITHABORT setting which causes problem&#8230;this is covered in this article.

Here is the whole outline

 **Introduction**
        
Presumptions
     
**How SQL Server Compiles a Stored Procedure**
        
What is a Stored Procedure?
        
How SQL Server Generates the Query Plan
        
Putting the Query Plan into the Cache
        
Different Plans for Different Settings
        
The Default Settings
        
The Effects of Statement Recompile
        
The Story So Far
     
**It&#8217;s Not Always Parameter Sniffing&#8230;**
        
Replacing Variables and Parameters
        
Blocking
        
Indexed Views and Indexed Computed Columns
     
**Getting Information to Solve Parameter Sniffing Problems**
        
Getting the Necessary Facts
        
Which is the Slow Statement?
        
Getting the Query Plans and Parameters with Management Studio
        
Getting the Query Plans and Parameters Directly from the Plan Cache
        
Getting Query Plans and Parameters from a Trace
        
Getting Table and Index Definitions
        
Finding Information About Statistics
     
**Examples of How to Fix Parameter-Sniffing Issues**
        
A Non-Solution
        
Best Index Depends on Input
        
Dynamic Search Conditions
        
Reviewing Indexing
        
The Case of the Application Cache
        
Fixing Bad SQL
     
**How SQL Server Compiles Dynamic SQL**
        
What Is Dynamic SQL?
        
Generating the Plan for Dynamic SQL
        
Dynamic SQL and the Plan Cache
        
Running Application Queries in SSMS
        
Addressing Parameter-sniffing Problems in Dynamic SQL
        
Plan Guides
     
**Further Reading**

You can find the article here: [Slow in the Application, Fast in SSMS?
  
Understanding Performance Mysteries][2]&#8230;.enjoy

 [1]: http://www.sommarskog.se/dynamic_sql.html
 [2]: http://www.sommarskog.se/query-plan-mysteries.html