---
title: What does a SQL Server developer need to know?
author: SQLDenis
type: post
date: 2011-02-21T23:27:00+00:00
ID: 1049
excerpt: I have been interviewing people for a SQL Server position for the past six weeks and all I can say that I am glad it is over. What a frustrating experience, people with over 10 years' experience could not tell me the difference between UNION and UNION A&hellip;
url: /index.php/datamgmt/datadesign/what-does-a-sql-server/
views:
  - 28299
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - how to
  - skills
  - sql server 2005
  - sql server 2008

---
I have been interviewing people for a SQL Server position for the past six weeks and all I can say that I am glad it is over. What a frustrating experience, people with over 10 years' experience could not tell me the difference between UNION and UNION ALL, most of the people never heard of TRUNCATE. We finally got our guy and he starts tomorrow.
  
So I would like to ask you the reader what a SQL Server developer should know when he falls into these levels.

Beginner — < 2 years Intermediate -- between 2 and 5 years Advanced –- over 5 years Here is what I think it should be, leave me a comment if you have something to add or disagree. I am only focusing on T-SQL here, no SSIS, SSRS, Powershell etc etc etc **Beginner — < 2 years**
  
Aggregates: COUNT, SUM, MAX/MIN, DISTINCT, GROUP BY, HAVING
  
JOINs, ANSI-89 and ANSI-92 syntax, Full, Outer, Inner
  
UNION vs UNION ALL
  
NULL handling: COALESCE/ISNULL and IS NULL
  
Subqueries: IN, EXISTS, and inline views, Correlated Subqueries
  
Constraints, Primary keys, foreign keys, defaults
  
Normalization
  
Basic stored procedures and user defined functions programming

**Intermediate — between 2 and 5 years**
  
Everything for the previous level plus
  
Dynamic SQL and parameterized queries
  
Deadlock, how to detect and how to avoid them
  
Windowing functions and CTEs
  
Execution plans and what they mean, how to read them
  
Profiler: Creating a trace, trace events and how to save a trace
  
Trapping errors
  
Isolation levels
  
Transactions: rollback, commit, using XACT_ABORT, Try, Catch
  
What a SARGable query is and how to avoid non SARGable queries
  
Truncate, BCP, BULK INSERT
  
Difference between clustered index and non clustered index
  
Triggers and how to write triggers that affect multirow operations
  
Advanced stored procedures and user defined functions programming
  
Advanced data modeling, cascade delete.
  
Linked servers
  
How to avoid conversions and how to choose the correct data types

**Advanced – over 5 years**
  
Everything for the previous two levels level plus
  
Parameter sniffing
  
Advanced indexing
  
Partitioned functions
  
Settings like ANSI_NULLS, ARITHABORT and how they can affect execution plans
  
Using Dynamic Management Views to tune your application
  
Indexed Views and the use of NOEXPAND in standard edition
  
Query and table hints
  
Concurrency and locking

I am sure I forgot a ton of stuff, leave me a comment if you think I placed a skill in the wrong skill level, also leave me a comment if you want to add something I have forgotten</ 2></ 2></ 2></ 2></ 2></ 2>