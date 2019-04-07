---
title: 'MongoDB: How to drop databases and collections'
author: SQLDenis
type: post
date: 2013-02-05T08:47:00+00:00
ID: 1968
excerpt: |
  In this post we are going to look at how to drop database and collections. We already covered backup and restores, now that you know how to do that, it is safe to cover dropping collections and databases
  
  Execute the following command, it will create&hellip;
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/mongodb-how-to-drop-databases/
views:
  - 15529
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - Microsoft SQL Server Admin
tags:
  - bigdata
  - collections
  - databases
  - mongodb
  - nosql

---
In this post we are going to look at how to drop database and collections. We already covered backup and restores, now that you know how to do that, it is safe to cover dropping collections and databases

Execute the following command, it will create the MultiCollection database if it doesn&#8217;t exist already

<pre>use MultiCollection</pre>

Now create these two collections

<pre>db.Blog.insert( { name : "Denis",  age : 20 } )
db.Blog.insert( { name : "Abe",    age : 30 } )
db.Blog.insert( { name : "John",   age : 40 } )
db.Blog.insert( { name : "Xavier", age : 10 } )
db.Blog.insert( { name : "Zen",    age : 50 } )


db.People.insert( { name : "AADenis",  age : 0020 } )
db.People.insert( { name : "AAAbe",    age : 0030 } )
db.People.insert( { name : "AAJohn",   age : 0040 } )
db.People.insert( { name : "AAXavier", age : 0010 } )
db.People.insert( { name : "AAZen",    age : 0050 } )</pre>

Now it is time to drop a collection. The syntax is pretty simple it is `db.CollectionName.drop()`. So if we wanted to drop the Blog collection it would be `db.Blog.drop()`

Time to try it out, run the following

<pre>db.Blog.drop()</pre>

The output you get back is `true`

Now if you do a find on the collection, you won&#8217;t get anything back

<pre>db.Blog.find()</pre>

That was pretty simple. Now let&#8217;s see how to drop a database. This is pretty easy as well, it is just `db.dropDatabase()`. I wished you had to specify the name of the database because you could be in the wrong database when executing the command

Execute this

<pre>db.dropDatabase()</pre>

Here is the output

<pre>{ "dropped" : "MultiCollection", "ok" : 1 }</pre>

Just be aware that you are still in the MultiCollection database, you can verify this by executing the following command

<pre>db.getName()</pre>

And here is the output

<pre>MultiCollection</pre>

If you now try to do a find for either collection that existed before you won&#8217;t get anything back

<pre>db.People.find()
db.Blog.find()</pre>

Doing a stats command on the database will tell you how many collections you have as well

<pre>db.stats()</pre>

Here is the output

<pre>{
        "db" : "MultiCollection",
        "collections" : 0,
        "objects" : 0,
        "avgObjSize" : 0,
        "dataSize" : 0,
        "storageSize" : 0,
        "numExtents" : 0,
        "indexes" : 0,
        "indexSize" : 0,
        "fileSize" : 0,
        "nsSizeMB" : 0,
        "ok" : 1
}</pre>

As you can see everything is pretty much 0

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