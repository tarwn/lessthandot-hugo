---
title: 'MongoDB: How to restore collections'
author: SQLDenis
type: post
date: 2013-01-31T20:08:00+00:00
ID: 1953
excerpt: "In yesterday's post MongoDB: How to backup and restore databases we looked at how to backup and restore a database, today we are going to look at how to restore a collection from a backup. Be aware that mongorestore and mongodump have to be executed fro&hellip;"
url: /index.php/datamgmt/dbadmin/mongodb-how-to-restore-collections/
views:
  - 14864
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
tags:
  - backup
  - bigdata
  - collections
  - mongodb
  - nosql
  - restore

---
In yesterday&#8217;s post [MongoDB: How to backup and restore databases][1] we looked at how to backup and restore a database, today we are going to look at how to restore a collection from a backup. Be aware that mongorestore and mongodump have to be executed from a command window in the bin directory where mongodb is. To execute the MongoDB commands you need to connect to mongodb first. On my PC this is in the directory C:NoSQLmongodbbin>

In order to get started create a new database, name it MultiCollection 

You can just run this command, it will switch to the MultiCollection database if it exists or otherwise it will create the MultiCollection database

<pre>use MultiCollection</pre>

Now add these two collections

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

Open a new command window, navigate to your mongodb bin directory. Now execute the `mongodump --db MultiCollection` command
  
This command will backup the database into the dump directory, if this directory does not exist it will be created, in my case it will be located here C:NoSQLmongodbbindump. In the dump directory you will see a directory with the same name as the database that you are backing up

<pre>mongodump --db MultiCollection</pre>

Here is the output

<pre>connected to: 127.0.0.1
Thu Jan 31 16:16:52 DATABASE: MultiCollection    to     dump/MultiCollection
Thu Jan 31 16:16:52     MultiCollection.Blog to dump/MultiCollection/Blog.bson
Thu Jan 31 16:16:52              5 objects
Thu Jan 31 16:16:52     Metadata for MultiCollection.Blog to dump/MultiCollection/Blog.metadata.json
Thu Jan 31 16:16:52     MultiCollection.People to dump/MultiCollection/People.bson
Thu Jan 31 16:16:52              5 objects
Thu Jan 31 16:16:52 Metadata for MultiCollection.People to dump/MultiCollection/People.metadata.json

C:NoSQLmongodbbin></pre>

Now let&#8217;s add one more item to the People collection

<pre>db.People.insert( { name : "ZZZZZZ",  age : 9999 } )</pre>

Time to do the restore

<pre>mongorestore dump/MultiCollection</pre>

Here is the output

<pre>connected to: 127.0.0.1
Thu Jan 31 16:20:52 dump/MultiCollection/Blog.bson
Thu Jan 31 16:20:52     going into namespace [MultiCollection.Blog]
Thu Jan 31 16:20:52 warning: Restoring to MultiCollection.Blog without dropping. Restored data will be inserted
raising errors; check your server log
5 objects found
Thu Jan 31 16:20:52     Creating index: { key: { _id: 1 }, ns: "MultiCollection.Blog", name: "_id_" }
Thu Jan 31 16:20:52 dump/MultiCollection/People.bson
Thu Jan 31 16:20:52     going into namespace [MultiCollection.People]
5 objects found
Thu Jan 31 16:20:52     Creating index: { key: { _id: 1 }, ns: "MultiCollection.People", name: "_id_" }</pre>

Let&#8217;s take a look at what we have
  
We should have 5 items since we did a backup before we added item 6, we restored that backup

<pre>db.People.find()</pre>

Output is here

<pre>{ "_id" : ObjectId("510adec2d9a67956d3f4a44b"), "name" : "AADenis", "age" : 16 }
{ "_id" : ObjectId("510adec2d9a67956d3f4a44c"), "name" : "AAAbe", "age" : 24 }
{ "_id" : ObjectId("510adec2d9a67956d3f4a44d"), "name" : "AAJohn", "age" : 32 }
{ "_id" : ObjectId("510adec2d9a67956d3f4a44e"), "name" : "AAXavier", "age" : 8 }
{ "_id" : ObjectId("510adec5d9a67956d3f4a44f"), "name" : "AAZen", "age" : 40 }
{ "_id" : ObjectId("510ae0b8d9a67956d3f4a451"), "name" : "ZZZZZZ", "age" : 9999 }</pre>

Something is not right, we still have 6 items. If you look back at the restore output, you will see the following warning

> warning: Restoring to MultiCollection.Blog without dropping. Restored data will be inserted
  
> raising errors; check your server log

What we have to do is drop the collections first, you do that by specifying `--drop`

Let&#8217;s do the restore again but now with the &#8211;drop option

<pre>mongorestore dump/MultiCollection --drop</pre>

Here is the output 

<pre>connected to: 127.0.0.1
Thu Jan 31 16:25:26 dump/MultiCollection/Blog.bson
Thu Jan 31 16:25:26     going into namespace [MultiCollection.Blog]
Thu Jan 31 16:25:26      dropping
5 objects found
Thu Jan 31 16:25:26     Creating index: { key: { _id: 1 }, ns: "MultiCollection.Blog", name: "_id_" }
Thu Jan 31 16:25:26 dump/MultiCollection/People.bson
Thu Jan 31 16:25:26     going into namespace [MultiCollection.People]
Thu Jan 31 16:25:26      dropping
5 objects found
Thu Jan 31 16:25:26     Creating index: { key: { _id: 1 }, ns: "MultiCollection.People", name: "_id_" }</pre>

<pre>db.People.find()</pre>

Here are the results

<pre>{ "_id" : ObjectId("510adec2d9a67956d3f4a44b"), "name" : "AADenis", "age" : 16 }
{ "_id" : ObjectId("510adec2d9a67956d3f4a44c"), "name" : "AAAbe", "age" : 24 }
{ "_id" : ObjectId("510adec2d9a67956d3f4a44d"), "name" : "AAJohn", "age" : 32 }
{ "_id" : ObjectId("510adec2d9a67956d3f4a44e"), "name" : "AAXavier", "age" : 8 }
{ "_id" : ObjectId("510adec5d9a67956d3f4a44f"), "name" : "AAZen", "age" : 40 }</pre>

As you can see we have 5 items now

Let&#8217;s insert the same item again

<pre>db.People.insert( { name : "ZZZZZZ",  age : 9999 } )</pre>

Let&#8217;s also drop the Blog collection

<pre>db.Blog.drop()</pre>

Now if you do a find nothing is there

<pre>db.Blog.find()</pre>

In order to restore a collection, you need to use the &#8211;collection option and give the collection name, you also need to specify where the backup file is. In our case it is dump/Multicollection/Blog.bson. You will also see that information from the dump output
  


> MultiCollection.Blog to dump/MultiCollection/Blog.bson</p>
Run the following

<pre>mongorestore --db MultiCollection --collection Blog dump/Multicollection/Blog.bson</pre>

Here is the output

<pre>connected to: 127.0.0.1
Thu Jan 31 17:01:28 dump/Multicollection/Blog.bson
Thu Jan 31 17:01:28     going into namespace [MultiCollection.Blog]
5 objects found
Thu Jan 31 17:01:28     Creating index: { key: { _id: 1 }, ns: "MultiCollection.Blog", name: "_id_" }</pre>

Since we already dropped the collection manually we didn&#8217;t have to add the drop option. Let&#8217;s see what we have in the database now, People should have 6 items and Blog should have 5 items

<pre>db.Blog.find()
{ "_id" : ObjectId("510adec1d9a67956d3f4a446"), "name" : "Denis", "age" : 20 }
{ "_id" : ObjectId("510adec1d9a67956d3f4a447"), "name" : "Abe", "age" : 30 }
{ "_id" : ObjectId("510adec2d9a67956d3f4a448"), "name" : "John", "age" : 40 }
{ "_id" : ObjectId("510adec2d9a67956d3f4a449"), "name" : "Xavier", "age" : 10 }
{ "_id" : ObjectId("510adec2d9a67956d3f4a44a"), "name" : "Zen", "age" : 50 }

db.People.find()
{ "_id" : ObjectId("510adec2d9a67956d3f4a44b"), "name" : "AADenis", "age" : 16 }
{ "_id" : ObjectId("510adec2d9a67956d3f4a44c"), "name" : "AAAbe", "age" : 24 }
{ "_id" : ObjectId("510adec2d9a67956d3f4a44d"), "name" : "AAJohn", "age" : 32 }
{ "_id" : ObjectId("510adec2d9a67956d3f4a44e"), "name" : "AAXavier", "age" : 8 }
{ "_id" : ObjectId("510adec5d9a67956d3f4a44f"), "name" : "AAZen", "age" : 40 }
{ "_id" : ObjectId("510ae75cd9a67956d3f4a453"), "name" : "ZZZZZZ", "age" : 9999 }</pre>

As you can see the People collection did not get overwritten and the Blog collection got restored

That is all for this post, if you are interested in my other MongoDB posts, you can find them here:
  
[Install MongoDB as a Windows Service][2]
  
[UPSERTs with MongoDB][3]
  
[How to sort results in MongoDB][4]
  
[Indexes in MongoDB: A quick overview][5]
  
[Multidocument updates with MongoDB][6]
  
[MongoDB: How to include and exclude the fields you want in results][7]
  
[MongoDB: How to limit results and how to page through results][8]
  
[MongoDB: How to backup and restore databases][1]

 [1]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/mongodb-backup-and-restore-databases
 [2]: /index.php/DataMgmt/DBProgramming/creating-mongodb-as-a-service
 [3]: /index.php/DataMgmt/DBProgramming/doing-upserts-in-mongodb
 [4]: /index.php/DataMgmt/DBProgramming/mongodb-how-to-sort-results
 [5]: /index.php/DataMgmt/DBProgramming/indexes-in-mongodb
 [6]: /index.php/DataMgmt/DBProgramming/multidocument-updates-with-mongodb
 [7]: /index.php/DataMgmt/DBProgramming/mongodb-how-to-include-and
 [8]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/mongodb-how-to-limit-results