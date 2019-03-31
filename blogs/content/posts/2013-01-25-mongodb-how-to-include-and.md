---
title: 'MongoDB: How to include and exclude the fields you want in results'
author: SQLDenis
type: post
date: 2013-01-25T11:54:00+00:00
excerpt: |
  In this MongoDB post we are going to look at how to return only the fields you want. We already looked at how to sort the results in the MongoDB, how to sort results but we didn't show you how to return just the fields you want.
  
  To get started insert&hellip;
url: /index.php/datamgmt/dbprogramming/mongodb-how-to-include-and/
views:
  - 20033
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
tags:
  - bigdata
  - filtering
  - mongodb
  - nosql
  - sorting

---
In this MongoDB post we are going to look at how to return only the fields you want. We already looked at how to sort the results in the [MongoDB, how to sort results][1] post but we didn&#8217;t show you how to return just the fields you want.

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

To return the name and not age or city, you would execute this

<pre>db.Blog.find(null, {name: 1});</pre>

Here are the results

<pre>{ "_id" : ObjectId("51028ae0a8c33b71ed76a807"), "name" : "Denis" }
{ "_id" : ObjectId("51028ae0a8c33b71ed76a808"), "name" : "Abe" }
{ "_id" : ObjectId("51028ae2a8c33b71ed76a809"), "name" : "John" }
{ "_id" : ObjectId("51028ae2a8c33b71ed76a80a"), "name" : "Xavier" }
{ "_id" : ObjectId("51028ae4a8c33b71ed76a80b"), "name" : "Zen" }</pre>

To return the name and age but not city, you would execute this

<pre>db.Blog.find(null, {name: 1, age: 1});</pre>

Here are the results

<pre>{ "_id" : ObjectId("51028ae0a8c33b71ed76a807"), "name" : "Denis", "age" : 20 }
{ "_id" : ObjectId("51028ae0a8c33b71ed76a808"), "name" : "Abe", "age" : 30 }
{ "_id" : ObjectId("51028ae2a8c33b71ed76a809"), "name" : "John", "age" : 40 }
{ "_id" : ObjectId("51028ae2a8c33b71ed76a80a"), "name" : "Xavier", "age" : 10 }
{ "_id" : ObjectId("51028ae4a8c33b71ed76a80b"), "name" : "Zen", "age" : 50 }</pre>

Do you notice that \_id is always returned? What if you want to exclude \_id from the results? You can exclude _id by adding `_id: 0`

Here is how you return just the name

<pre>db.Blog.find(null, {name: 1, _id: 0});</pre>

Results are below

<pre>{ "name" : "Denis" }
{ "name" : "Abe" }
{ "name" : "John" }
{ "name" : "Xavier" }
{ "name" : "Zen" }</pre>

Here is how you return just the name and the age

<pre>db.Blog.find(null, {name: 1, age: 1, _id: 0});</pre>

Here are the results

<pre>{ "name" : "Denis", "age" : 20 }
{ "name" : "Abe", "age" : 30 }
{ "name" : "John", "age" : 40 }
{ "name" : "Xavier", "age" : 10 }
{ "name" : "Zen", "age" : 50 }</pre>

As you can see this is a little different from SQL, if you in SQL do something like `SELECT name, age FROM Table`, you won&#8217;t get the id or primary key back in the results by default

If you are interested in my other MongoDB posts, you can find them here:
  
[Install MongoDB as a Windows Service][2]
  
[UPSERTs with MongoDB][3]
  
[How to sort results in MongoDB][1]
  
[Indexes in MongoDB: A quick overview][4]
  
[Multidocument updates with MongoDB][5]

 [1]: /index.php/DataMgmt/DBProgramming/mongodb-how-to-sort-results
 [2]: /index.php/DataMgmt/DBProgramming/creating-mongodb-as-a-service
 [3]: /index.php/DataMgmt/DBProgramming/doing-upserts-in-mongodb
 [4]: /index.php/DataMgmt/DBProgramming/indexes-in-mongodb
 [5]: /index.php/DataMgmt/DBProgramming/multidocument-updates-with-mongodb