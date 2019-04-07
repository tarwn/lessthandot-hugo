---
title: MongoDB, how to sort results
author: SQLDenis
type: post
date: 2013-01-16T08:38:00+00:00
ID: 1920
excerpt: |
  This is my third MongoDB post, in the first post we look at how we can install MongoDB as a Windows Service, In the second post we looked at how we could do UPSERTs with MongoDB, today we will look at how to sort results in MongoDB
  
  Connect to MongoDB&hellip;
url: /index.php/datamgmt/dbprogramming/mongodb-how-to-sort-results/
views:
  - 11193
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
tags:
  - bigdata
  - mongodb
  - nosql
  - sorting

---
This is my third MongoDB post, in the first post we look at how we can [install MongoDB as a Windows Service][1], In the second post we looked at how we could do [UPSERTs with MongoDB][2], today we will look at how to sort results in MongoDB

Connect to MongoDB and insert the following data

<pre>db.SortTest.insert( { name : "Denis", age : 20 } )
db.SortTest.insert( { name : "Abe", age : 30 } )
db.SortTest.insert( { name : "John", age : 40 } )
db.SortTest.insert( { name : "Xavier", age : 10 } )
db.SortTest.insert( { name : "Zen", age : 50 } )</pre>

Now run this command

<pre>db.SortTest.find()</pre>

You should get back something like the following, the ObjectId will be different for you
  
{ &#8220;_id&#8221; : ObjectId(&#8220;50f6811c03141917bce6459f&#8221;), &#8220;name&#8221; : &#8220;Denis&#8221;, &#8220;age&#8221; : 20 }
  
{ &#8220;_id&#8221; : ObjectId(&#8220;50f6811d03141917bce645a0&#8221;), &#8220;name&#8221; : &#8220;Abe&#8221;, &#8220;age&#8221; : 30 }
  
{ &#8220;_id&#8221; : ObjectId(&#8220;50f6811d03141917bce645a1&#8221;), &#8220;name&#8221; : &#8220;John&#8221;, &#8220;age&#8221; : 40 }
  
{ &#8220;_id&#8221; : ObjectId(&#8220;50f6811d03141917bce645a2&#8221;), &#8220;name&#8221; : &#8220;Xavier&#8221;, &#8220;age&#8221; : 10 }
  
{ &#8220;_id&#8221; : ObjectId(&#8220;50f6811e03141917bce645a3&#8221;), &#8220;name&#8221; : &#8220;Zen&#8221;, &#8220;age&#8221; : 50 }

Ordering in MongoDB is pretty easy, you add sort, then you specify the fields you want to sort on, use 1 for ascending and use -1 for descending

If we want to sort by name descending, we can use the following

<pre>db.SortTest.find().sort({name: -1})</pre>

Here are the results, as you can see they are sorted by name descending
  
{ &#8220;_id&#8221; : ObjectId(&#8220;50f6811e03141917bce645a3&#8221;), &#8220;name&#8221; : &#8220;Zen&#8221;, &#8220;age&#8221; : 50 }
  
{ &#8220;_id&#8221; : ObjectId(&#8220;50f6811d03141917bce645a2&#8221;), &#8220;name&#8221; : &#8220;Xavier&#8221;, &#8220;age&#8221; : 10 }
  
{ &#8220;_id&#8221; : ObjectId(&#8220;50f6811d03141917bce645a1&#8221;), &#8220;name&#8221; : &#8220;John&#8221;, &#8220;age&#8221; : 40 }
  
{ &#8220;_id&#8221; : ObjectId(&#8220;50f6811c03141917bce6459f&#8221;), &#8220;name&#8221; : &#8220;Denis&#8221;, &#8220;age&#8221; : 20 }
  
{ &#8220;_id&#8221; : ObjectId(&#8220;50f6811d03141917bce645a0&#8221;), &#8220;name&#8221; : &#8220;Abe&#8221;, &#8220;age&#8221; : 30 }

To sort by name ascending, you just have to change -1 to 1

<pre>db.SortTest.find().sort({name: 1})</pre>

Here are the results and the names are now ascending
  
{ &#8220;_id&#8221; : ObjectId(&#8220;50f6811d03141917bce645a0&#8221;), &#8220;name&#8221; : &#8220;Abe&#8221;, &#8220;age&#8221; : 30 }
  
{ &#8220;_id&#8221; : ObjectId(&#8220;50f6811c03141917bce6459f&#8221;), &#8220;name&#8221; : &#8220;Denis&#8221;, &#8220;age&#8221; : 20 }
  
{ &#8220;_id&#8221; : ObjectId(&#8220;50f6811d03141917bce645a1&#8221;), &#8220;name&#8221; : &#8220;John&#8221;, &#8220;age&#8221; : 40 }
  
{ &#8220;_id&#8221; : ObjectId(&#8220;50f6811d03141917bce645a2&#8221;), &#8220;name&#8221; : &#8220;Xavier&#8221;, &#8220;age&#8221; : 10 }
  
{ &#8220;_id&#8221; : ObjectId(&#8220;50f6811e03141917bce645a3&#8221;), &#8220;name&#8221; : &#8220;Zen&#8221;, &#8220;age&#8221; : 50 }

Here is age ascending

<pre>db.SortTest.find().sort( { age: -1 } );</pre>

Results
  
{ &#8220;_id&#8221; : ObjectId(&#8220;50f6811e03141917bce645a3&#8221;), &#8220;name&#8221; : &#8220;Zen&#8221;, &#8220;age&#8221; : 50 }
  
{ &#8220;_id&#8221; : ObjectId(&#8220;50f6811d03141917bce645a1&#8221;), &#8220;name&#8221; : &#8220;John&#8221;, &#8220;age&#8221; : 40 }
  
{ &#8220;_id&#8221; : ObjectId(&#8220;50f6811d03141917bce645a0&#8221;), &#8220;name&#8221; : &#8220;Abe&#8221;, &#8220;age&#8221; : 30 }
  
{ &#8220;_id&#8221; : ObjectId(&#8220;50f6811c03141917bce6459f&#8221;), &#8220;name&#8221; : &#8220;Denis&#8221;, &#8220;age&#8221; : 20 }
  
{ &#8220;_id&#8221; : ObjectId(&#8220;50f6811d03141917bce645a2&#8221;), &#8220;name&#8221; : &#8220;Xavier&#8221;, &#8220;age&#8221; : 10 }

Here is age descending

<pre>db.SortTest.find().sort( { age: 1 } );</pre>

{ &#8220;_id&#8221; : ObjectId(&#8220;50f6811d03141917bce645a2&#8221;), &#8220;name&#8221; : &#8220;Xavier&#8221;, &#8220;age&#8221; : 10 }
  
{ &#8220;_id&#8221; : ObjectId(&#8220;50f6811c03141917bce6459f&#8221;), &#8220;name&#8221; : &#8220;Denis&#8221;, &#8220;age&#8221; : 20 }
  
{ &#8220;_id&#8221; : ObjectId(&#8220;50f6811d03141917bce645a0&#8221;), &#8220;name&#8221; : &#8220;Abe&#8221;, &#8220;age&#8221; : 30 }
  
{ &#8220;_id&#8221; : ObjectId(&#8220;50f6811d03141917bce645a1&#8221;), &#8220;name&#8221; : &#8220;John&#8221;, &#8220;age&#8221; : 40 }
  
{ &#8220;_id&#8221; : ObjectId(&#8220;50f6811e03141917bce645a3&#8221;), &#8220;name&#8221; : &#8220;Zen&#8221;, &#8220;age&#8221; : 50 }

Let&#8217;s do another insert, this one has the same name but a different age

<pre>db.SortTest.insert( { name : "Abe", age : 50 } )</pre>

Sorting by name again

<pre>db.SortTest.find().sort({name: -1})</pre>

As you can see Abe is there twice
  
{ &#8220;_id&#8221; : ObjectId(&#8220;50f6811e03141917bce645a3&#8221;), &#8220;name&#8221; : &#8220;Zen&#8221;, &#8220;age&#8221; : 50 }
  
{ &#8220;_id&#8221; : ObjectId(&#8220;50f6811d03141917bce645a2&#8221;), &#8220;name&#8221; : &#8220;Xavier&#8221;, &#8220;age&#8221; : 10 }
  
{ &#8220;_id&#8221; : ObjectId(&#8220;50f6811d03141917bce645a1&#8221;), &#8220;name&#8221; : &#8220;John&#8221;, &#8220;age&#8221; : 40 }
  
{ &#8220;_id&#8221; : ObjectId(&#8220;50f6811c03141917bce6459f&#8221;), &#8220;name&#8221; : &#8220;Denis&#8221;, &#8220;age&#8221; : 20 }
  
{ &#8220;_id&#8221; : ObjectId(&#8220;50f6811d03141917bce645a0&#8221;), &#8220;name&#8221; : &#8220;Abe&#8221;, &#8220;age&#8221; : 30 }
  
{ &#8220;_id&#8221; : ObjectId(&#8220;50f6818103141917bce645a4&#8221;), &#8220;name&#8221; : &#8220;Abe&#8221;, &#8220;age&#8221; : 50 }

Here is how you do a multi-field sort, it is pretty much the same as in SQL, you just add the other field and specify 1 or -1

So let&#8217;s sort by age descending and name ascending

<pre>db.SortTest.find().sort( { age: -1 , name: 1} );</pre>

Here are the results
  
{ &#8220;_id&#8221; : ObjectId(&#8220;50f6818103141917bce645a4&#8221;), &#8220;name&#8221; : &#8220;Abe&#8221;, &#8220;age&#8221; : 50 }
  
{ &#8220;_id&#8221; : ObjectId(&#8220;50f6811e03141917bce645a3&#8221;), &#8220;name&#8221; : &#8220;Zen&#8221;, &#8220;age&#8221; : 50 }
  
{ &#8220;_id&#8221; : ObjectId(&#8220;50f6811d03141917bce645a1&#8221;), &#8220;name&#8221; : &#8220;John&#8221;, &#8220;age&#8221; : 40 }
  
{ &#8220;_id&#8221; : ObjectId(&#8220;50f6811d03141917bce645a0&#8221;), &#8220;name&#8221; : &#8220;Abe&#8221;, &#8220;age&#8221; : 30 }
  
{ &#8220;_id&#8221; : ObjectId(&#8220;50f6811c03141917bce6459f&#8221;), &#8220;name&#8221; : &#8220;Denis&#8221;, &#8220;age&#8221; : 20 }
  
{ &#8220;_id&#8221; : ObjectId(&#8220;50f6811d03141917bce645a2&#8221;), &#8220;name&#8221; : &#8220;Xavier&#8221;, &#8220;age&#8221; : 10 }

Now, we are going to sort by age ascending and name descending

<pre>db.SortTest.find().sort( { age: 1 , name: -1} );</pre>

{ &#8220;_id&#8221; : ObjectId(&#8220;50f6811d03141917bce645a2&#8221;), &#8220;name&#8221; : &#8220;Xavier&#8221;, &#8220;age&#8221; : 10 }
  
{ &#8220;_id&#8221; : ObjectId(&#8220;50f6811c03141917bce6459f&#8221;), &#8220;name&#8221; : &#8220;Denis&#8221;, &#8220;age&#8221; : 20 }
  
{ &#8220;_id&#8221; : ObjectId(&#8220;50f6811d03141917bce645a0&#8221;), &#8220;name&#8221; : &#8220;Abe&#8221;, &#8220;age&#8221; : 30 }
  
{ &#8220;_id&#8221; : ObjectId(&#8220;50f6811d03141917bce645a1&#8221;), &#8220;name&#8221; : &#8220;John&#8221;, &#8220;age&#8221; : 40 }
  
{ &#8220;_id&#8221; : ObjectId(&#8220;50f6811e03141917bce645a3&#8221;), &#8220;name&#8221; : &#8220;Zen&#8221;, &#8220;age&#8221; : 50 }
  
{ &#8220;_id&#8221; : ObjectId(&#8220;50f6818103141917bce645a4&#8221;), &#8220;name&#8221; : &#8220;Abe&#8221;, &#8220;age&#8221; : 50 }

As you can see sorting is very easy to do in MongoDB. One thing to keep in mind

> The sort function requires that the entire sort be able to complete within 32 megabytes. When the sort option consumes more than 32 megabytes, MongoDB will return an error. Use cursor.limit(), or create an index on the field that you’re sorting to avoid this error.

We are going to look at how to create indexes in the next post: [Indexes in MongoDB: A quick overview][3]

For more MongoDB posts, take a look at these

[Install MongoDB as a Windows Service][1]
  
[UPSERTs with MongoDB][2]
  
[How to sort results in MongoDB][4]
  
[Indexes in MongoDB: A quick overview][3]
  
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
 [3]: /index.php/DataMgmt/DBProgramming/indexes-in-mongodb
 [4]: /index.php/DataMgmt/DBProgramming/mongodb-how-to-sort-results
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