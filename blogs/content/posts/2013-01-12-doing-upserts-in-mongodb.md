---
title: Doing UPSERTs in MongoDB
author: SQLDenis
type: post
date: 2013-01-12T13:14:00+00:00
ID: 1911
excerpt: "This post will explain how to do UPSERT statements in MongoDB. Before we start let's first describe what an UPSERT statement is. An UPSERT statement will insert a row if it doesn't exist already, if the row does exist then it will update the row. In SQL&hellip;"
url: /index.php/datamgmt/dbprogramming/doing-upserts-in-mongodb/
views:
  - 19424
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
This post will explain how to do UPSERT statements in MongoDB. Before we start let's first describe what an UPSERT statement is. An UPSERT statement will insert a row if it doesn't exist already, if the row does exist then it will update the row. In SQL Server or Oracle you would use the [MERGE][1] statement. In MongDB, you can supply the _upsert_ option in the<options> argument. We will take a look at how that all works later in this post.</options>

Open up a new command prompt or shell, connect to MongoDB and either use an existing database or create a new one

```cmd
C:UsersDenis>C:NoSQLmongodbbinmongo
MongoDB shell version: 2.2.2
connecting to: test
> use UpsertTest
switched to db UpsertTest
```

If you need some help with setting up MongoDB on Windows 7 or Windows 8, take a look at [Creating MongoDB as a service on Windows 8][2]

Let's do a simple insert, run the following

<pre>db.things.insert( { name : "Denis1"} )</pre>

Now let's bring back what we just inserted

<pre>db.things.find({name : "Denis1"})</pre>

Here is the result
  
{ "_id" : ObjectId("50f1778ea5ec290b7773303b"), "name" : "Denis1" }

Just a warning, the ObjectId which is 50f1778ea5ec290b7773303b here will be different on your system.

If we want to update that from Denis1 to Denis2, we can use the following

<pre>db.things.update({name: "Denis1"}, {name: "Denis2"})</pre>

Now if you run this again, you won't get anything back

<pre>db.things.find({name : "Denis1"})</pre>

If instead you use Denis2, you will get the row back

<pre>db.things.find({name : "Denis2"})</pre>

{ "_id" : ObjectId("50f1778ea5ec290b7773303b"), "name" : "Denis2" }

Just to make sure that you have only one thing in the collection run the following

<pre>db.things.find()</pre>

You should back one thing only
  
{ "_id" : ObjectId("50f1778ea5ec290b7773303b"), "name" : "Denis2" }

Time to look at the upsert command
  
When you run the following, Denis6 will be inserted, Denis5 does not exist, there is nothing to update so Denis6 gets inserted

<pre>db.things.update({name : "Denis5"}, {name: "Denis6"}, true);</pre>

If you do a search for Denis6, you will get it back from the collection

<pre>db.things.find({name : "Denis6"})</pre>

{ "_id" : ObjectId("50f1793d41c33bd2459aafeb"), "name" : "Denis6" }

Instead of just specifying true in the options you can also name it to make it more clear, it will look like this { upsert: true }

Run this command, it will update Denis6 to Denis7

<pre>db.things.update({name : "Denis6"}, {name: "Denis7"}, { upsert: true });</pre>

<pre>db.things.find({name : "Denis7"})</pre>

{ "_id" : ObjectId("50f1793d41c33bd2459aafeb"), "name" : "Denis7" }

## Upsert example with an incremented counter

in this part we will use the $inc operator. The $inc operator increments a value by a specified amount if field is present in the document. If the field does not exist, $inc sets field to the number value.

Let's get started, let's say we want to track how many hits a certain page gets, let's say this page is named _How To Do Upserts_, everytime someone hits that page we will increment the hits counter. Here is what we will execute

<pre>db.things.update({BlogPost: "How To Do Upserts"}, {$inc: {Hits: 1}}, { upsert: true });</pre>

Now if we look in the collection

<pre>db.things.find({BlogPost : "How To Do Upserts"})</pre>

Here is what is returned, as you can see hits is 1
  
{ "_id" : ObjectId("50f17b4541c33bd2459aafed"), "BlogPost" : "How To Do Upserts", "Hits" : 1 }
  
Let's run that same update statement two more times

<pre>db.things.update({BlogPost: "How To Do Upserts"}, {$inc: {Hits: 1}}, { upsert: true });
db.things.update({BlogPost: "How To Do Upserts"}, {$inc: {Hits: 1}}, { upsert: true });</pre>

Now if we look in the collection again

<pre>db.things.find({BlogPost : "How To Do Upserts"})</pre>

{ "_id" : ObjectId("50f17b4541c33bd2459aafed"), "BlogPost" : "How To Do Upserts", "Hits" : 3 }

As you can see the hits counter has now increased to 3.

Here is also the whole command window output in case you find it easier to follow along like that

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/mongo/MongoDBCOnsoleOutput.PNG?mtime=1358002899"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/mongo/MongoDBCOnsoleOutput.PNG?mtime=1358002899" width="682" height="507" /></a>
</div>

That is all for this post, in the next post we will look at some more interesting stuff

 [1]: /index.php/DataMgmt/DBProgramming/MSSQLServer/sql-advent-2011-day-10
 [2]: /index.php/DataMgmt/DBProgramming/creating-mongodb-as-a-service