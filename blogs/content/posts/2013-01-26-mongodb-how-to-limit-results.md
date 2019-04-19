---
title: 'MongoDB: How to limit results and how to page through results'
author: SQLDenis
type: post
date: 2013-01-26T12:41:00+00:00
ID: 1938
excerpt: 'In this post we are going to take a look at how to limit results in MongoDB as well how to page through results. MongoDB use limit to limit the number of results return, MongoDB use skip to skip a number of records from the results set. Using limit in c&hellip;'
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/mongodb-how-to-limit-results/
views:
  - 27468
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server Admin
tags:
  - bigdata
  - mongodb
  - nosql
  - paging
  - sorting

---
In this post we are going to take a look at how to limit results in MongoDB as well how to page through results. MongoDB use limit to limit the number of results return, MongoDB use skip to skip a number of records from the results set. Using limit in conjunction with skip enables you to do paging in MongoDB.

Let's take a look at how this works

To get started insert the following into your MongoDB database

<pre>db.Blog.insert( { name : "Denis",  age : 20, city : "Princeton" } )
db.Blog.insert( { name : "Abe",    age : 30, city : "Amsterdam" } )
db.Blog.insert( { name : "John",   age : 40, city : "New York"  } )
db.Blog.insert( { name : "Xavier", age : 10, city : "Barcelona" } )
db.Blog.insert( { name : "Zen",    age : 50, city : "Kyoto"     } )</pre>

To return everything, you would execute the following

<pre>db.Blog.find()</pre>

Here are the results

<pre>{ "_id" : ObjectId("51028ae0a8c33b71ed76a807"), "name" : "Denis", "age" : 20, "city" : "Princeton" }
{ "_id" : ObjectId("51028ae0a8c33b71ed76a808"), "name" : "Abe", "age" : 30, "city" : "Amsterdam" }
{ "_id" : ObjectId("51028ae2a8c33b71ed76a809"), "name" : "John", "age" : 40, "city" : "New York" }
{ "_id" : ObjectId("51028ae2a8c33b71ed76a80a"), "name" : "Xavier", "age" : 10, "city" : "Barcelona" }
{ "_id" : ObjectId("51028ae4a8c33b71ed76a80b"), "name" : "Zen", "age" : 50, "city" : "Kyoto" }</pre>

## Limiting results in MongoDB

To return just the first 2 items from the collection

<pre>db.Blog.find().limit(2)</pre>

Here are the results

<pre>{ "_id" : ObjectId("5103e22c88a39c3c0b2585e1"), "name" : "Denis", "age" : 20, "city" : "Princeton" }
{ "_id" : ObjectId("5103e22d88a39c3c0b2585e2"), "name" : "Abe", "age" : 30, "city" : "Amsterdam" }</pre>

That are indeed the first two, but what if we want to have it sorted by name?
  
In that case we can add sort, run the following

<pre>db.Blog.find().sort({name: 1}).limit(2)</pre>

Here are the results

<pre>{ "_id" : ObjectId("5103e22d88a39c3c0b2585e2"), "name" : "Abe", "age" : 30, "city" : "Amsterdam" }
{ "_id" : ObjectId("5103e22c88a39c3c0b2585e1"), "name" : "Denis", "age" : 20, "city" : "Princeton" }</pre>

We can of course only return name and nothing else, this was described in the post [MongoDB: How to include and exclude the fields you want in results][1]

Run the following

<pre>db.Blog.find(null, {name: 1, _id: 0}).sort({name: 1}).limit(2)</pre>

Here are the results

<pre>{ "name" : "Abe" }
{ "name" : "Denis" }</pre>

## Paging in MongoDB

Now, let's take a look at how we can return items 3 and 4, this would be John and Xavier. In this case we need to use skip, all we have to add is skip(2)

Here is what the command looks like, run the following

<pre>db.Blog.find(null, {name: 1, _id: 0}).sort({name: 1}).limit(2).skip(2)</pre>

And here are the results

<pre>{ "name" : "John" }
{ "name" : "Xavier" }</pre>

If you pass in values that are outside of the number of items in the collection, you won't get an error but neither will you get results. If you were to skip 6 items, you won't get anything back

<pre>db.Blog.find(null, {name: 1, _id: 0}).sort({name: 1}).limit(2).skip(6)</pre>

What if you want to return the items in the collection in the order that they were inserted onto disk? You can use the $natural parameter, the $natural parameter returns items according to their order on disk. If you use -1, you can get them returned in the reverse order

<pre>db.Blog.find().sort( { $natural: -1 } )</pre>

Here are the results 

<pre>{ "_id" : ObjectId("5103eaa688a39c3c0b2585ed"), "name" : "Zen", "age" : 50, "city" : "Kyoto" }
{ "_id" : ObjectId("5103eaa588a39c3c0b2585ec"), "name" : "Xavier", "age" : 10, "city" : "Barcelona" }
{ "_id" : ObjectId("5103eaa588a39c3c0b2585eb"), "name" : "John", "age" : 40, "city" : "New York" }
{ "_id" : ObjectId("5103eaa588a39c3c0b2585ea"), "name" : "Abe", "age" : 30, "city" : "Amsterdam" }
{ "_id" : ObjectId("5103eaa588a39c3c0b2585e9"), "name" : "Denis", "age" : 20, "city" : "Princeton" }</pre>

Just a warning when using sort(), be aware of the following limitation

> Warning The sort function requires that the entire sort be able to complete within 32 megabytes. When the sort option consumes more than 32 megabytes, MongoDB will return an error. Use cursor.limit(), or create an index on the field that you're sorting to avoid this error.

That is all for this post, if you are interested in my other MongoDB posts, you can find them here:
  
[Install MongoDB as a Windows Service][2]
  
[UPSERTs with MongoDB][3]
  
[How to sort results in MongoDB][4]
  
[Indexes in MongoDB: A quick overview][5]
  
[Multidocument updates with MongoDB][6]
  
[MongoDB: How to include and exclude the fields you want in results][1]

 [1]: /index.php/DataMgmt/DBProgramming/mongodb-how-to-include-and
 [2]: /index.php/DataMgmt/DBProgramming/creating-mongodb-as-a-service
 [3]: /index.php/DataMgmt/DBProgramming/doing-upserts-in-mongodb
 [4]: /index.php/DataMgmt/DBProgramming/mongodb-how-to-sort-results
 [5]: /index.php/DataMgmt/DBProgramming/indexes-in-mongodb
 [6]: /index.php/DataMgmt/DBProgramming/multidocument-updates-with-mongodb