---
title: Surrogate Keys Bite Back
author: David Forck (thirster42)
type: post
date: 2012-08-15T17:31:00+00:00
excerpt: 'Not too long ago I got a work order in that was requesting help with removing duplicate records out of a report.  This report, and the database it was built upon, was built solely by a co-worker that left two years ago.  This report just displayed a lis&hellip;'
url: /index.php/datamgmt/datadesign/surrogate-keys-bite-back/
views:
  - 6446
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
Not too long ago I got a work order in that was requesting help with removing duplicate records out of a report. This report, and the database it was built upon, was built solely by a co-worker that left two years ago. This report just displayed a list of project numbers and some data related to them. I opened up the project table and voila! I found some duplicate projects. I noticed that the table had an identity field that was the primary key, but the project number didn’t have a unique index on it, leaving the table open to allow someone to put in the same project multiple times, either on purpose or accident.

I dug further, and found the query that the report was running. The query was joining several different tables together, but not on the primary keys. Rather, the joins were on different fields in the tables that should have been unique in their respective tables, but weren’t. This was causing reporting issues, but didn’t seem to cause any issues in the application itself, which I found interesting.

So, looking through the database, I collected a list of things that were wrong:
  
1. Not using the primary key column (the id column) as the foreign key in child tables
  
2. Not using unique indexes on fields that obviously weren’t supposed to have duplicates in the table
  
3. Joining on non-pk/fk fields expecting them to act like pk/fk fields

So the takeaway from this is, if you’re going to have surrogate keys (i.e. id columns) in a table, you need to utilize them so that you get proper joining logic. You should also have unique indexes to enforce the logic that one entity (ie a project number, employee, etc.) can only have one record.