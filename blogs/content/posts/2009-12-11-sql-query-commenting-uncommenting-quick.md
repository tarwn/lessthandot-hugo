---
title: SQL Query Commenting/Uncommenting Quick Tip
author: Erik
type: post
date: 2009-12-11T17:28:37+00:00
ID: 647
url: /index.php/datamgmt/dbprogramming/sql-query-commenting-uncommenting-quick/
views:
  - 10876
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server

---
If you have multiple queries in one SQL query file, and you are switching around between which ones you want to run at any one time, there is an easy way to do this.

The scenario I'm thinking of is something like when you're solving a SQL puzzle/challenge and keep trying new ideas and query versions. You don't want to delete your old queries because they are useful for reference or to copy and paste parts of them, but you don't want to run them all the time. Especially during performance tweaking, you want to be able to quickly comment out the worst queries and keep pasting in new versions of the fastest queries.

Normally this would require either highlighting entire queries to comment/uncomment them (Ctrl-Shift-C/Ctrl-Shift-U in Query Analyzer or Ctrl-K:Ctrl-C/Ctrl-K:Ctrl-U in SSMS); adding or removing /\* and \*/ at the top and bottom of the query; or highlighting just the portions of the code that you want to run each time, which is a pain and can be complicated if you are using table variables since your initialization code at the top has to run each time.

So try this!

Put &#8211;/\* at the top of your query and &#8211;\*/ at the bottom:

sql
--/* Test Query #3 (Gorp Method)
SELECT *
FROM Blah
WHERE Gorp = 1
--*/
```

Notice that the query is NOT commented out.

Now, to toggle the whole query as commented or not, just uncomment the first line:

sql
/* Test Query #3 (Gorp Method)
SELECT *
FROM Blah
WHERE Gorp = 1
--*/
```

I just hit on this method yesterday and I'm really enjoying it. It provides a natural place to put a query comment, you only have to put the block comments in once, and then you can use your comment/uncomment shortcuts on just a single line to toggle an entire block of code! Just keep in mind that the action IS reversed: you comment to uncomment and you uncomment to comment.