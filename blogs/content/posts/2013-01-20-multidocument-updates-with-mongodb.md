---
title: Multidocument updates with MongoDB
author: SQLDenis
type: post
date: 2013-01-20T15:16:00+00:00
ID: 1926
excerpt: |
  This is my fifth MongoDB posts, you can find all the MongoDB posts here http://blogs.lessthandot.com/index.php/All/mongodb:
  
  Today we are going to look at update statements. We looked at it a little already in the Doing UPSERTs in MongoDB post, in thi&hellip;
url: /index.php/datamgmt/dbprogramming/multidocument-updates-with-mongodb/
views:
  - 8334
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
tags:
  - bigdata
  - mongodb
  - nosql
  - update

---
This is my fifth MongoDB post, you can find all the MongoDB posts here /index.php/All/mongodb:

Today we are going to look at update statements. We looked at it a little already in the [Doing UPSERTs in MongoDB][1] post, in this post I want to show you how updates are different from regular SQL.

To get started insert this data

<pre>db.Indexing.insert( { name : "Denis", age : 10 } )
db.Indexing.insert( { name : "Denis", age : 20 } )
db.Indexing.insert( { name : "Denis", age : 30 } )
db.Indexing.insert( { name : "Abe", age : 10 } )
db.Indexing.insert( { name : "Abe", age : 20 } )
db.Indexing.insert( { name : "Abe", age : 30 } )
</pre>

Now let's say I want to update the age for all documents that have the name Denis to 40. You would think it would be this

<pre>db.Indexing.update({name: "Denis"}, {age: 40})</pre>

Execute that and let's bring back all the documents with the name Denis

<pre>db.Indexing.find({name: "Denis"})</pre>

Here are the results

<pre>{ "_id" : ObjectId("50fc234dfda7317c756fb846"), "name" : "Denis", "age" : 20 }
{ "_id" : ObjectId("50fc234dfda7317c756fb847"), "name" : "Denis", "age" : 30 }</pre>

Where is the the document with the name Denis for which I changed the age to 40? Let's bring back all documents in this collection to find out

<pre>db.Indexing.find()</pre>

<pre>{ "_id" : ObjectId("50fc234dfda7317c756fb845"), "age" : 40 }
{ "_id" : ObjectId("50fc234dfda7317c756fb846"), "name" : "Denis", "age" : 20 }
{ "_id" : ObjectId("50fc234dfda7317c756fb847"), "name" : "Denis", "age" : 30 }
{ "_id" : ObjectId("50fc234dfda7317c756fb848"), "name" : "Abe", "age" : 10 }
{ "_id" : ObjectId("50fc234dfda7317c756fb849"), "name" : "Abe", "age" : 20 }
{ "_id" : ObjectId("50fc234dfda7317c756fb84a"), "name" : "Abe", "age" : 30 }</pre>

Oops, see what happened, it replaced `"name" : "Denis", "age" : 20` to `"age" : 40`
  
Yikes, not what we wanted. So how do you do it then? We can use the $set operator to set a particular value

The syntax looks like this

<pre>db.collection.update( { field: value1 }, { $set: { field1: value2 } } );</pre>

Let's try again

<pre>db.Indexing.update({name: "Denis"}, {$set: {age: 40}})</pre>

Let's bring back both of the documents where the name is Denis

<pre>db.Indexing.find({name: "Denis"})</pre>

Here are the results

<pre>{ "_id" : ObjectId("50fc234dfda7317c756fb846"), "name" : "Denis", "age" : 40 }
{ "_id" : ObjectId("50fc234dfda7317c756fb847"), "name" : "Denis", "age" : 30 }</pre>

What happened? As you can see only 1 document got updated. The default behavior of the update() method updates a single document and would correspond to the SQL UPDATE statement with LIMIT 1 or TOP 1. With the multi option, update() method would correspond to the SQL UPDATE statement without the LIMIT or TOP clause.

All we have to add is multi: true, here is how we would have to rewrite our update

<pre>db.Indexing.update({name: "Denis"}, {$set: {age: 42}},{ multi: true })</pre>

Let's bring back both of the documents where the name is Denis again

<pre>db.Indexing.find({name: "Denis"})</pre>

Here are the results

<pre>{ "_id" : ObjectId("50fc234dfda7317c756fb846"), "name" : "Denis", "age" : 42 }
{ "_id" : ObjectId("50fc234dfda7317c756fb847"), "name" : "Denis", "age" : 42 }</pre>

As you can see both have now 42 for age

Be aware of these differences compared to traditional RDBMS systems when doing updates, you might be wrecking your data without realizing it immediately……… and when you do realize it, it might be too late 🙁

 [1]: /index.php/DataMgmt/DBProgramming/doing-upserts-in-mongodb