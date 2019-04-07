---
title: How to Delete Large amounts of data in a Batch.
author: ptheriault
type: post
date: 2011-01-20T11:56:00+00:00
excerpt: 'Recently I had a request to DELETE all the data except the last 30 days of data from a log table that is 80 GB in size.  It sounded like a simple request on the surface.  However, I couldn’t shut down the web service that logs to that table every 5 minu&hellip;'
url: /index.php/datamgmt/datadesign/how-to-delete-large-amounts/
views:
  - 10204
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server

---
Recently I had a request to DELETE all the data except the last 30 days of data from a log table that is 80 GB in size.  It sounded like a simple request on the surface.  However, I couldn’t shut down the web service that logs to that table every 5 minutes.  So I had to get the delete done between inserts.  So, my only choice was to run the delete in small batches.  That’s when I stumbled upon this nice little trick.  New to 2005 and 2008 you can place a number after your GO statement and it will execute the batch that many times.  So, I was able delete records from my log table 10000 at a time without causing issues for the Web Service.

For Example:

<pre>DECLARE @Timestamp datetime
DELETE TOP (10000) from  Log With (tablockx, holdlock)
WHERE Timestamp < @Timestamp
GO 500</pre>