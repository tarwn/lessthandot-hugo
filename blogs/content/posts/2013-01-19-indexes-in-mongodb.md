---
title: 'Indexes in MongoDB: A quick overview'
author: SQLDenis
type: post
date: 2013-01-19T12:23:00+00:00
ID: 1923
excerpt: |
  This is my Fourth MongoDB post, in the first post we looked at how we can install MongoDB as a Windows Service, In the second post we looked at how we could do UPSERTs with MongoDB, In the third post we looked at how to sort results in MongoDB
  This pos&hellip;
url: /index.php/datamgmt/dbprogramming/indexes-in-mongodb/
views:
  - 13064
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
tags:
  - bigdata
  - indexes
  - indexing
  - mongodb
  - nosql

---
This is my fourth MongoDB post, in the first post we looked at how we can [install MongoDB as a Windows Service][1], In the second post we looked at how we could do [UPSERTs with MongoDB][2], In the third post we looked at [how to sort results in MongoDB][3]. This post is about indexing in MongoDB, we are going to take a look at how to create indexes and how to see if the indexes are being used by MongoDB.
  
Every index that you create in MongoDB is a secondary index, this is because MongoDB creates the default \_id index for all collections. The \_id index is a unique index on the \_id field, you cannot delete the index on \_id.
  
MongoDB indexes use a B-tree data structure

Besides a regular one field index, you can also create the following indexes in MongoDB

  * Indexes on Embedded Fields
  * Compound Indexes
  * Multikey Indexes
  * Unique Indexes
  * Sparse Indexes

Before you go crazy and start adding indexes on every possible field in your collection, keep in mind that just like in regular databases, the more indexes you have the slower you write operations will be. Every update and insert will be a little slower because the indexes will have to be maintained.

Some limitations:
  
A collection can&#8217;t have more than 64 indexes.
  
Index keys can&#8217;t be larger than 1024 bytes. This includes the field value or values, the field name or names, and the namespace.

Let&#8217;s go take a look at some of these indexes

## Creating A Simple Index in MongoDB

Let&#8217;s insert some data before we create the index

<pre>db.Indexing.insert( { name : "Denis", age : 20 } )
db.Indexing.insert( { name : "Abe", age : 30 } )
db.Indexing.insert( { name : "John", age : 40 } )
db.Indexing.insert( { name : "Xavier", age : 10 } )
db.Indexing.insert( { name : "Zen", age : 50 } )</pre>

Now let&#8217;s run a simple query that will return all rows where the name is Denis and we want to see what the engine is doing. You can use explain to return the plan

<pre>db.Indexing.find({name: "Denis"}).explain()</pre>

Here is what you get back

<pre>{
        "cursor" : "BasicCursor",
        "isMultiKey" : false,
        "n" : 0,
        "nscannedObjects" : 0,
        "nscanned" : 0,
        "nscannedObjectsAllPlans" : 0,
        "nscannedAllPlans" : 0,
        "scanAndOrder" : false,
        "indexOnly" : false,
        "nYields" : 0,
        "nChunkSkips" : 0,
        "millis" : 0,
        "indexBounds" : {

        },
        "server" : "Denis:27017"
}</pre>

Let&#8217;s add an index

<pre>db.Indexing.ensureIndex({name: 1});</pre>

What we did was, we created an index on name and it is sorted ascending, if you want descending, you would use -1 instead of 1

Now if we add run the same command from before with explain

<pre>db.Indexing.find({name: "Denis"}).explain()</pre>

Here are the results

<pre>{
        "cursor" : "BtreeCursor name_1",
        "isMultiKey" : false,
        "n" : 1,
        "nscannedObjects" : 1,
        "nscanned" : 1,
        "nscannedObjectsAllPlans" : 1,
        "nscannedAllPlans" : 1,
        "scanAndOrder" : false,
        "indexOnly" : false,
        "nYields" : 0,
        "nChunkSkips" : 0,
        "millis" : 1,
        "indexBounds" : {
                "name" : [
                        [
                                "Denis",
                                "Denis"
                        ]
                ]
        },
        "server" : "Denis:27017"
}</pre>

As you can see several things are different. Instead of a BasicCursor, we now use a BtreeCursor

Also instead of

<pre>"indexBounds" : {
        
}</pre>

we now see

<pre>"indexBounds" : {
      "name" : [
                 [
                      "Denis",
                      "Denis"
                 ]
               ]
    },</pre>

## Dropping an Index in MongoDB

You can drop an index by specifying a field in a collection

<pre>db.Indexing.dropIndex({name: 1});</pre>

Here is the output

<pre>{ "nIndexesWas" : 2, "ok" : 1 }</pre>

You can also drop all indexes for a collection in one shot

<pre>db.Indexing.dropIndexes()</pre>

Here is the output

<pre>{
        "nIndexesWas" : 1,
        "msg" : "non-_id indexes dropped for collection",
        "ok" : 1
}</pre>

As we mentioned before all _id fields have an index by default and those don&#8217;t get dropped

## Creating A Unique Index in MongoDB

Let&#8217;s now create an index on name again but this time we will make it unique., The syntax is the same as with the index we created before with the addition _unique: true_
  
If you didn&#8217;t drop the index we created before, drop it first and then execute the following

<pre>db.Indexing.ensureIndex({name: 1}, {unique: true});</pre>

Now if we try to insert the same name again, you will see that we get an error

<pre>db.Indexing.insert( { name : "Denis", age : 20 } )</pre>

Here is the output
  
_E11000 duplicate key error index: Indexing.Indexing.$name_1 dup key: { : &#8220;Denis&#8221; }_

As you can see it is pretty easy to create a unique index

## Creating A Compound Index in MongoDB

Creating an compound index is pretty straightforward as well, you just add the other fields. So if we want to create an compound index on name and age then you would do it like this

<pre>db.Indexing.ensureIndex({name: 1, age : 1});</pre>

Just like in a regular index, you use 1 for ascending and -1 for descending

To create an compound unique index, you would just add _unique: true_

<pre>db.Indexing.ensureIndex({name: 1, age : 1}, {unique: true})</pre>

The same rules like in a regular RDBMS when an compound index is used apply. If you supply the first field ot the first field and subsequent fields, then the index will be used, otherwise the index will not be used. You can easily test this.

Search for name which is the first field in the compound index

<pre>db.Indexing.find({name: "Denis"}).explain()</pre>

Here is the output, as you can see the index is used

<pre>{
        "cursor" : "BtreeCursor name_1_age_1",
        "isMultiKey" : false,
        "n" : 1,
        "nscannedObjects" : 1,
        "nscanned" : 1,
        "nscannedObjectsAllPlans" : 1,
        "nscannedAllPlans" : 1,
        "scanAndOrder" : false,
        "indexOnly" : false,
        "nYields" : 0,
        "nChunkSkips" : 0,
        "millis" : 0,
        "indexBounds" : {
                "name" : [
                        [
                                "Denis",
                                "Denis"
                        ]
                ],
                "age" : [
                        [
                                {
                                        "$minElement" : 1
                                },
                                {
                                        "$maxElement" : 1
                                }
                        ]
                ]
        },
        "server" : "Denis:27017"
}</pre>

Now search for name which is the second field in the index

<pre>db.Indexing.find({age: "20"}).explain()</pre>

Here is the output for that

<pre>{
        "cursor" : "BasicCursor",
        "isMultiKey" : false,
        "n" : 0,
        "nscannedObjects" : 5,
        "nscanned" : 5,
        "nscannedObjectsAllPlans" : 5,
        "nscannedAllPlans" : 5,
        "scanAndOrder" : false,
        "indexOnly" : false,
        "nYields" : 0,
        "nChunkSkips" : 0,
        "millis" : 0,
        "indexBounds" : {

        },
        "server" : "Denis:27017"
}</pre>

As you can see, no index was used

* * *

If you want to dive deeper into Indexing in MongoDB I would suggest you take a look at the documentation on the MongoDB site, they have an excellent section on Indexing with many more examples than I have given here. I didn&#8217;t cover Sparse Indexes, Indexes on Embedded Fields, Multikey Indexes, TTL Indexes, Geospatial Indexes or Geohaystack Indexes. You can find the documentation here: http://docs.mongodb.org/manual/core/indexes/

 [1]: /index.php/DataMgmt/DBProgramming/creating-mongodb-as-a-service
 [2]: /index.php/DataMgmt/DBProgramming/doing-upserts-in-mongodb
 [3]: /index.php/DataMgmt/DBProgramming/mongodb-how-to-sort-results