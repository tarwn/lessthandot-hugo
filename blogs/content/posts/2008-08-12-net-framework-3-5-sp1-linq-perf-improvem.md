---
title: '.NET Framework 3.5 SP1: LINQ perf improvements (LINQ to Objects and LINQ to SQL)'
author: SQLDenis
type: post
date: 2008-08-12T10:56:32+00:00
ID: 106
url: /index.php/datamgmt/datadesign/net-framework-3-5-sp1-linq-perf-improvem/
views:
  - 4071
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - linq
  - performance

---
Dinesh wrote a blogpost about .[NET Framework 3.5 SP1: LINQ perf improvements (LINQ to Objects and LINQ to SQL)][1]. There are three perf improvements in the just released SP1

> Specialized enumerable: The new implementation recognizes queries that apply Where and/or Select to arrays or List<T>s and fold pipelines of multiple enumerable objects into single specialized enumerables. This produces substantial improvement in base overhead of common LINQ to Objects queries (at times 30+%).

Make sure to check it out!

 [1]: http://blogs.msdn.com/dinesh.kulkarni/archive/2008/08/10/net-fx-3-5-sp1-two-perf-improvements-linq-to-objects-and-linq-to-sql.aspx