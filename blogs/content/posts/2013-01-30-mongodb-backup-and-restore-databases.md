---
title: 'MongoDB: How to backup and restore databases'
author: SQLDenis
type: post
date: 2013-01-30T17:13:00+00:00
ID: 1948
excerpt: 'Today it is time to learn how to backup and restore databases in MongoDB. You do have jobs setup that automatically create backups right? If you do not, please close this window and go set that up first, your data is the most important part of the organ&hellip;'
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/mongodb-backup-and-restore-databases/
views:
  - 68355
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server Admin
tags:
  - backup
  - bigdata
  - mongodb
  - nosql
  - restore

---
Today it is time to learn how to backup and restore databases in MongoDB. You do have jobs setup that automatically create backups right? If you do not, please close this window and go set that up first, your data is the most important part of the organization, without data you got nothing, just ask [ma.gnolia][1]. That site is not around anymore because they didn't back up their database. Enough of that let's get started.
  
Connect to mongodb, create a new database named blog. You can do that by just executing `use blog`

Now insert the following 5 items in that database

<pre>db.Blog.insert( { name : "Denis",  age : 20, city : "Princeton" } )
db.Blog.insert( { name : "Abe",    age : 30, city : "Amsterdam" } )
db.Blog.insert( { name : "John",   age : 40, city : "New York"  } )
db.Blog.insert( { name : "Xavier", age : 10, city : "Barcelona" } )
db.Blog.insert( { name : "Zen",    age : 50, city : "Kyoto"     } )</pre>

You have now a collection named Blog with those 5 items.

If you want to list all the collections for a database, you can use `db.getCollectionNames()`

<pre>db.getCollectionNames()</pre>

Here is the output

<pre>[ "Blog", "system.indexes" ]</pre>

Let's get some stats for that collection

<pre>db.Blog.stats()</pre>

Here is the output

<pre>{
        "ns" : "blog.Blog",
        "count" : 5,
        "size" : 356,
        "avgObjSize" : 71.2,
        "storageSize" : 8192,
        "numExtents" : 1,
        "nindexes" : 1,
        "lastExtentSize" : 8192,
        "paddingFactor" : 1,
        "systemFlags" : 1,
        "userFlags" : 0,
        "totalIndexSize" : 8176,
        "indexSizes" : {
                "_id_" : 8176
        },
        "ok" : 1
}</pre>

## Backing Up

Time to do the backup. To do a backup you can't run if you are connected to mongodb. Open up a new command/shell window, navigate to the bin directory inside the mongodb folder. In my case this is D:mongodbbin. To do a backup we are going to call the mongodump executable inside the bin directory. Here is what the syntax will look like

mongodump &#8211;db {Database name} 

You need to change the database name to something that you have. Running the command will create a directory named dump

<pre>mongodump --db blog</pre>

Here is what the output looks like

<pre>connected to: 127.0.0.1
Wed Jan 30 13:55:56 DATABASE: blog       to     dump/blog
Wed Jan 30 13:55:56     blog.Blog to dump/blog/Blog.bson
Wed Jan 30 13:55:56              5 objects
Wed Jan 30 13:55:56     Metadata for blog.Blog to dump/blog/Blog.metadata.json

D:mongodbbin></pre>

The Blog.bson file is the actual backup of the database

As you can see the backup also creates a metadata file Blog.metadata.json

All that the manifest file has in it is the following
  
{ “indexes” : [ { “v” : 1, “key” : { “\_id” : 1 }, “ns” : “blog.Blog”, “name” : “\_id_” } ] }

As you can see it has the database as well as the collection name

We will talk more about the .bson file in tomorrow's post

Now that we have a backup we can do a restore. But first let's drop the collection 🙂

Execute the following in the command window connected to mongodb

<pre>db.Blog.drop()</pre>

Here is the output

<pre>true</pre>

Now if we check the stats again, there will be an error

<pre>db.Blog.stats()</pre>

<pre>{ "errmsg" : "ns not found", "ok" : 0 }</pre>

## Restore

Now it is time to do a restore, use the command/shell window where you did the backup. The restore process uses an executable as well that needs to be executed from a command window outside the MongoDB process.

The command will look like the following

`mongorestore directory/database`
  
In our case the command will look like the following

`mongorestore dump/blog`

Execute the following

<pre>mongorestore dump/blog</pre>

Here is what the output looks like

<pre>connected to: 127.0.0.1
Wed Jan 30 13:59:58 dump/blog/Blog.bson
Wed Jan 30 13:59:58     going into namespace [blog.Blog]
5 objects found
Wed Jan 30 13:59:58     Creating index: { key: { _id: 1 }, ns: "blog.Blog", name: "_id_" }</pre>

Now, let's see if we have the collection back

<pre>db.Blog.stats()</pre>

<pre>{
        "ns" : "blog.Blog",
        "count" : 5,
        "size" : 356,
        "avgObjSize" : 71.2,
        "storageSize" : 8192,
        "numExtents" : 1,
        "nindexes" : 1,
        "lastExtentSize" : 8192,
        "paddingFactor" : 1,
        "systemFlags" : 1,
        "userFlags" : 0,
        "totalIndexSize" : 8176,
        "indexSizes" : {
                "_id_" : 8176
        },
        "ok" : 1
}</pre>

As you can see the collection is there again, the restore worked.

You can also restore the backup to a new database or another database. If the database does not exists it will create one for you. If I run the same command from before but use &#8211;db RestoredDB you will get the following output

<pre>mongorestore -db RestoredDB dump/blog
connected to: 127.0.0.1
Wed Jan 30 14:05:26 dump/blog/Blog.bson
Wed Jan 30 14:05:26     going into namespace [RestoredDB.Blog]
5 objects found
Wed Jan 30 14:05:26     Creating index: { key: { _id: 1 }, ns: "RestoredDB.Blog", name: "_id_" }
</pre>

Now connecting to MongoDB again we can check that the new database is there with the same collection

<pre>use RestoredDB
switched to db RestoredDB
db.Blog.find()
{ "_id" : ObjectId("510961b1e5247980b903c30c"), "name" : "Denis", "age" : 20, "city" : "Princeton" }
{ "_id" : ObjectId("510961b3e5247980b903c30d"), "name" : "Abe", "age" : 30, "city" : "Amsterdam" }
{ "_id" : ObjectId("510961b3e5247980b903c30e"), "name" : "John", "age" : 40, "city" : "New York" }
{ "_id" : ObjectId("510961b3e5247980b903c30f"), "name" : "Xavier", "age" : 10, "city" : "Barcelona"
{ "_id" : ObjectId("510961b5e5247980b903c310"), "name" : "Zen", "age" : 50, "city" : "Kyoto" }</pre>

As you can see it it pretty straightforward to backup and restore databases. What if you just want to restore a collection? Aha…that will be tomorrow's post: [MongoDB: How to restore collections][2]

That is all for this post, if you are interested in my other MongoDB posts, you can find them here:
  
[Install MongoDB as a Windows Service][3]
  
[UPSERTs with MongoDB][4]
  
[How to sort results in MongoDB][5]
  
[Indexes in MongoDB: A quick overview][6]
  
[Multidocument updates with MongoDB][7]
  
[MongoDB: How to include and exclude the fields you want in results][8]
  
[MongoDB: How to limit results and how to page through results][9]

 [1]: /index.php/ITProfessionals/EthicsIT/magnolia-crashed-data-corruption-and-los
 [2]: /index.php/DataMgmt/DBAdmin/mongodb-how-to-restore-collections
 [3]: /index.php/DataMgmt/DBProgramming/creating-mongodb-as-a-service
 [4]: /index.php/DataMgmt/DBProgramming/doing-upserts-in-mongodb
 [5]: /index.php/DataMgmt/DBProgramming/mongodb-how-to-sort-results
 [6]: /index.php/DataMgmt/DBProgramming/indexes-in-mongodb
 [7]: /index.php/DataMgmt/DBProgramming/multidocument-updates-with-mongodb
 [8]: /index.php/DataMgmt/DBProgramming/mongodb-how-to-include-and
 [9]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/mongodb-how-to-limit-results