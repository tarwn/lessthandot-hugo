---
title: MongoDB 2.4 Released
author: SQLDenis
type: post
date: 2013-03-20T07:51:00+00:00
ID: 2040
excerpt: |
  MongoDB 2.4 has been released. MongoDB 2.4 is the latest stable release, following the September 2012 release of MongoDB 2.2. The MongoDB 2.4 release contains key new features along with performance improvements and bug fixes.
  
  Here are the highlights&hellip;
url: /index.php/datamgmt/dbprogramming/mongodb-2-4-released/
views:
  - 11864
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
tags:
  - bigdata
  - mongodb
  - nosql

---
MongoDB 2.4 has been released. MongoDB 2.4 is the latest stable release, following the September 2012 release of MongoDB 2.2. The MongoDB 2.4 release contains key new features along with performance improvements and bug fixes.

Here are the highlights

**Hash-based Sharding**: MongoDB 2.4 adds Hash-based Sharding, built on top of our range based sharding. Using a hashed shard key allows users to get a good distribution of load and data in a simple manner, in cases where documents are accessed randomly through the key space, or if the access patterns may not be totally predictable.

**Capped Arrays:** Capped arrays declare a fixed size array inside of a document. On a $push operation, users can now specify a $slice modifier, which trims the array to the last N items. You can also specify a sort, which will first sort the array, and then apply the trim.

**Text Search:** Text Search has been one of the all time most requested features in MongoDB. Text indexing will offer native, real-time text search with stemming and tokenization in 15 languages. For more details on Text Search and its implementation see the docs and blog post.

**Geo Capabilities**: MongoDB 2.4 introduces GeoJSON support, a more accurate spherical model and enhanced search including polygon intersection. Currently 2dsphere supports the Point, LineString and Polygon GeoJSON shapes. 

**Faster Counts:** In many cases, counts in MongoDB 2.4 are an order of magnitude faster than previous versions. We made numerous optimizations to the query execution engine in order to improve common access patterns. One example is in a single b-tree bucket: if the first and last entry in the bucket match a count range, we know the middle keys do as well, thus we do not have to check them individually.

**Working Set Analyzer:** Capacity planning is critical to running a MongoDB cluster. In MongoDB 2.4 we added a working set size analyzer, making it easy to measure the percentage of resources used. It will tell you how many unique pages the server has needed in the last 15 minutes, so that you can track usage over time. When the amount of data needed in 15 minutes is approaching RAM, its probably time to add more capacity to your cluster.

**New V8 Engine:** MongoDB 2.4 changed the JavaScript engine used for MapReduce, $where and the shell. We have switched to V8, the JavaScript engine from Google Chrome, which improves concurrency.

**Security:** MongoDB 2.4 two major security enhancements: Kerberos Authentication and Role Based Access Control. Kerberos is part of MongoDB Enterprise and allows integration with enterprise level user management systems. Role Based Access Control allows more fine grained privilege management. 

You can read the release notes here: http://docs.mongodb.org/manual/release-notes/2.4/

MongoDB 2.4 can be downloaded here: http://www.mongodb.org/downloads

I have written a bunch of MongoDB posts, here is a list:
  
[Install MongoDB as a Windows Service][1]
  
[UPSERTs with MongoDB][2]
  
[How to sort results in MongoDB][3]
  
[Indexes in MongoDB: A quick overview][4]
  
[Multidocument updates with MongoDB][5]
  
[MongoDB: How to include and exclude the fields you want in results][6]
  
[MongoDB: How to limit results and how to page through results][7]
  
[MongoDB: How to backup and restore databases][8]
  
[MongoDB: How to restore collections][9]
  
[MongoDB: How to backup all the databases with one command][10]
  
[MongoDB: Exporting data into files][11]
  
[MongoDB: How to drop databases and collections][12]
  
[MongoDB: Creating capped collections][13]
  
[MongoDB: Returning documents where fields are null or not existing][14]
  
[MongoDB: Using the web-based administrative tool][15]

 [1]: /index.php/DataMgmt/DBProgramming/creating-mongodb-as-a-service
 [2]: /index.php/DataMgmt/DBProgramming/doing-upserts-in-mongodb
 [3]: /index.php/DataMgmt/DBProgramming/mongodb-how-to-sort-results
 [4]: /index.php/DataMgmt/DBProgramming/indexes-in-mongodb
 [5]: /index.php/DataMgmt/DBProgramming/multidocument-updates-with-mongodb
 [6]: /index.php/DataMgmt/DBProgramming/mongodb-how-to-include-and
 [7]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/mongodb-how-to-limit-results
 [8]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/mongodb-backup-and-restore-databases
 [9]: /index.php/DataMgmt/DBAdmin/mongodb-how-to-restore-collections
 [10]: /index.php/DataMgmt/DBAdmin/mongodb-how-to-backup-all
 [11]: /index.php/DataMgmt/DBProgramming/mongodb-exporting-data-into-files
 [12]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/mongodb-how-to-drop-databases
 [13]: /index.php/DataMgmt/DBProgramming/mongodb-creating-capped-collections
 [14]: /index.php/DataMgmt/DBProgramming/mongodb-returning-documents-where-fields
 [15]: /index.php/DataMgmt/DBAdmin/mongodb-using-the-web-based