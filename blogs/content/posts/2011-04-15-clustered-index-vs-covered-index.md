---
title: Clustered index vs. Covered index for Range Scans.
author: SQLArcher
type: post
date: -001-11-30T00:00:00+00:00
ID: 1117
excerpt: 'Recently I read a post if clustered indexes are best for range queries, or not. After copying the code, creating the tables, etc. it was apparent that the logic was not correct, and instead proved nothing more than using the correct index for the job.&hellip;'
draft: true
url: /?p=1117
categories:
  - Data Modelling and Design
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
Recently I read a post if clustered indexes are best for range queries, or not. After copying the code, creating the tables, etc. it was apparent that the logic was not correct, and instead proved nothing more than using the correct index for the job.

The author came to the conclusion that the covering index outperforms the clustered index for range queries.

Table schema is as follows:

 

To properly test a clustered index vs. a covering non clustered index, we will need to provide equal ground.