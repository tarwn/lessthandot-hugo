---
title: 'sqltrace: A Tool Every SQL Server Developer Should Have'
author: SQLDenis
type: post
date: 2008-12-03T13:08:11+00:00
url: /index.php/datamgmt/datadesign/sqltrace-a-tool-every-sql-server-develop/
views:
  - 4457
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - performance tuning
  - sql server 2005
  - sql server 2008
  - tool
  - trace
  - tuning

---
**sqtlrace** is a stored procedure written by Lee Tudor (a.k.a Mr Tea.) The procedure takes an SQL batch as its first argument, sets up a trace filtered to include the current connection only, runs the batch, and summarises the trace information per statement in the batch. This can be a very handy tool to track down performance bottlenecks in a longer stored procedure, not the least if there are calls to nested procedures.

You can also use sqltrace to capture the query plans from a batch. This makes it possible to capture a single plan in a deep chain of nested stored procedures without drowning in all the other plans. This is particularly convenient with SSMS 2008: if you click on the XML document for the plan in grid mode, the graphical plans open directly.

Erland Sommarskog is hosting this on his site, you can get the source code and more info here: http://www.sommarskog.se/sqlutil/sqltrace.html