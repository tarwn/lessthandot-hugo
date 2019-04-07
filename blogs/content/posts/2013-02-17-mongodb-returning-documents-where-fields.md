---
title: 'MongoDB: Returning documents where fields are null or not existing'
author: SQLDenis
type: post
date: 2013-02-17T16:48:00+00:00
ID: 2004
excerpt: |
  In a regular SQL database, you can check if a column is null by using IS NULL
  For example if you wanted to return all rows where the age is null, you would do the following
  
  SELECT * 
  FROM SomeTable
  WHERE age IS NULL
  
  In a NoSQL database it is po&hellip;
url: /index.php/datamgmt/dbprogramming/mongodb-returning-documents-where-fields/
views:
  - 25128
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
tags:
  - bigdata
  - collections
  - mongodb
  - nosql
  - 'null'

---
In a regular SQL database, you can check if a column is null by using IS NULL
  
For example if you wanted to return all rows where the age is null, you would do the following

```sql
SELECT * 
FROM SomeTable
WHERE age IS NULL
```

In a NoSQL database it is possible that half the documents in a collection are omitted and maybe five are there with the value null. How can you know if the field is missing or has the value null?

Let&#8217;s take a quick look. First insert the following document into your collection

<pre>db.Blog.insert( { name : "Denis2" } )</pre>

As you can see it just has a name. Now let&#8217;s add another document this time with age as well, we will make the age NULL

<pre>db.Blog.insert( { name : "Denis",  age : NULL} )</pre>

You get the following error
  
Sun Feb 17 13:44:58 ReferenceError: NULL is not defined (shell):1

This error occurs because you need to pass null in lowercase

<pre>db.Blog.insert( { name : "Denis",  age : null } )</pre>

Now if you execute the following

<pre>db.Blog.find({age:null});</pre>

You get back both document
  
{ &#8220;_id&#8221; : ObjectId(&#8220;512118a7c1eca3d7ffcd00f9&#8221;), &#8220;name&#8221; : &#8220;Denis&#8221;, &#8220;age&#8221; : null }
  
{ &#8220;_id&#8221; : ObjectId(&#8220;512123d9c1eca3d7ffcd00fa&#8221;), &#8220;name&#8221; : &#8220;Denis2&#8221; }

In order to return the document where the value that is stored is null, you can use `$type: 10`. What that means is that the field is of BSON Type Null 

<pre>db.Blog.find( { age: { $type: 10 } } )</pre>

Here is the output

<pre>{ "_id" : ObjectId("512118a7c1eca3d7ffcd00f9"), "name" : "Denis", "age" : null }</pre>

In order to return the document where the field does not exist, you can use `$exists: false`

<pre>db.Blog.find( { age: { $exists: false } } )</pre>

Here is the output

<pre>{ "_id" : ObjectId("512123d9c1eca3d7ffcd00fa"), "name" : "Denis2" }</pre>

That is all for this post, if you are interested in my other MongoDB posts, you can find them here:
  
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