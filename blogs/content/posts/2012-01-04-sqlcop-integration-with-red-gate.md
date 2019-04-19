---
title: SQLCop integration with Red Gate's SQL Test
author: George Mastros (gmmastros)
type: post
date: 2012-01-04T18:52:00+00:00
ID: 1478
excerpt: 'Approximately a year and a half ago, friends of mine and I created SQLCop.  Our motivation was to provide a tool that users can download and run against their database.  This tool is very effective at detecting common problems with database configuratio&hellip;'
url: /index.php/datamgmt/datadesign/sqlcop-integration-with-red-gate/
views:
  - 6509
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - sql test
  - sqlcop
  - tsqlt

---
Approximately a year and a half ago, friends of mine and I created SQLCop. Our motivation was to provide a tool that users can download and run against their database. This tool is very effective at detecting common problems with database configurations and TSQL code. Not every issue highlighted by SQLCop requires a fix, but you are very likely to discover potential problems that you didn't even know you had.

About a year ago, I met Dennis Lloyd, Jr. and Sebastian Meine at a presentation they were giving for tSQLt ([a unit testing framework for SQL Server][1]) that they were working on. I was intrigued because I had always wanted to write unit tests for my tSQL code but hadn't been able to implement anything because of time constraints on my end. The tSQLt framework allowed me to forget about the mechanics of implementing and running the tests and allowed me to focus on writing the tests.

Several weeks ago, I was approached by Justin Caldicott at Red Gate Software. He informed that they had recently released SQL Test which is a SSMS add-in that makes writing and executing tSQLt tests easier. 

Justin was interested in including SQLCop tests within SQL Test. I downloaded and installed SQLTest. I was surprised to see that it recognized all of the tests that I had written manually. I also like how easy it is to run tests and create new ones. This tool has already saved me time.

I spent a relatively short amount of time re-writing a couple of the SQLCop tests to run within the tSQLt framework. At this point, there are several tests already written and included with SQL Test (Preview 2). 

If you haven't done so already, I encourage you to download and install [SQLCop][2]. I would be extremely surprised if you didn't find something useful with it.

I also encourage everyone to download and install [SQL Test][3] so that you can see for yourself how easy it is to run unit tests on your database.

 [1]: http://tSQLt.org
 [2]: http://sqlcop.ltd.local
 [3]: http://www.red-gate.com/products/sql-development/sql-test/