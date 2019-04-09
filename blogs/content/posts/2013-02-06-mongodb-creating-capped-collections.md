---
title: 'MongoDB: Creating capped collections'
author: SQLDenis
type: post
date: 2013-02-06T09:03:00+00:00
ID: 1971
excerpt: "Let's say you have a collection and you are only interested in storing the last 50 items or so. Everytime you add a new item, you want the oldest item to disappear. What you can do is create a capped collection. Here are some behaviors that capped colle&hellip;"
url: /index.php/datamgmt/dbprogramming/mongodb-creating-capped-collections/
views:
  - 10312
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
tags:
  - bigdata
  - collections
  - mongodb
  - nosql

---
Let's say you have a collection and you are only interested in storing the last 50 items or so. Everytime you add a new item, you want the oldest item to disappear. What you can do is create a capped collection. Here are some behaviors that capped collections have

> Capped collections guarantee preservation of the insertion order. As a result, queries do not need an index to return documents in insertion order. Without this indexing overhead, they can support higher insertion throughput.
> 
> Capped collections guarantee that insertion order is identical to the order on disk (natural order) and do so by prohibiting updates that increase document size. Capped collections only allow updates that fit the original document size, which ensures a document does not change its location on disk.
> 
> Capped collections automatically remove the oldest documents in the collection without requiring scripts or explicit remove operations.

Here is the syntax for creating a capped collection of one megabyte

<pre>db.createCollection('TestSize', {capped: true, size: 1048576})</pre>

Let's get started with some code, create a new database named TestCap

<pre>use TestCap</pre>

<pre>switched to db TestCap</pre>

Now instead of a megabyte capped collection, we are going to create a kilobyte capped collection

<pre>db.createCollection('TestSize', {capped: true, size: 1024})</pre>

<pre>{ "ok" : 1 }</pre>

Time to insert some data, we will make it simple by using a loop to insert 2000 items

<pre>for (var i = 1; i <= 2000; i++) db.TestSize.insert( { name : "denis" + i , age : i } )</pre>

Time to check what is in the collection, you can use `db.collection.find()` to iterate through the collection, it will return you 20 items, if you press `it` it will give you the next 20 items

<pre>db.TestSize.find()</pre>

Here is the output

<pre>{ "_id" : ObjectId("511232046ce24e7ea32698fb"), "name" : "denis1948", "age" : 1948 }
{ "_id" : ObjectId("511232046ce24e7ea32698fc"), "name" : "denis1949", "age" : 1949 }
{ "_id" : ObjectId("511232046ce24e7ea32698fd"), "name" : "denis1950", "age" : 1950 }
{ "_id" : ObjectId("511232046ce24e7ea32698fe"), "name" : "denis1951", "age" : 1951 }
{ "_id" : ObjectId("511232046ce24e7ea32698ff"), "name" : "denis1952", "age" : 1952 }
{ "_id" : ObjectId("511232046ce24e7ea3269900"), "name" : "denis1953", "age" : 1953 }
{ "_id" : ObjectId("511232046ce24e7ea3269901"), "name" : "denis1954", "age" : 1954 }
{ "_id" : ObjectId("511232046ce24e7ea3269902"), "name" : "denis1955", "age" : 1955 }
{ "_id" : ObjectId("511232046ce24e7ea3269903"), "name" : "denis1956", "age" : 1956 }
{ "_id" : ObjectId("511232046ce24e7ea3269904"), "name" : "denis1957", "age" : 1957 }
{ "_id" : ObjectId("511232046ce24e7ea3269905"), "name" : "denis1958", "age" : 1958 }
{ "_id" : ObjectId("511232046ce24e7ea3269906"), "name" : "denis1959", "age" : 1959 }
{ "_id" : ObjectId("511232046ce24e7ea3269907"), "name" : "denis1960", "age" : 1960 }
{ "_id" : ObjectId("511232046ce24e7ea3269908"), "name" : "denis1961", "age" : 1961 }
{ "_id" : ObjectId("511232046ce24e7ea3269909"), "name" : "denis1962", "age" : 1962 }
{ "_id" : ObjectId("511232046ce24e7ea326990a"), "name" : "denis1963", "age" : 1963 }
{ "_id" : ObjectId("511232046ce24e7ea326990b"), "name" : "denis1964", "age" : 1964 }
{ "_id" : ObjectId("511232046ce24e7ea326990c"), "name" : "denis1965", "age" : 1965 }
{ "_id" : ObjectId("511232046ce24e7ea326990d"), "name" : "denis1966", "age" : 1966 }
{ "_id" : ObjectId("511232046ce24e7ea326990e"), "name" : "denis1967", "age" : 1967 }
Type "it" for more
> it
{ "_id" : ObjectId("511232046ce24e7ea326990f"), "name" : "denis1968", "age" : 1968 }
{ "_id" : ObjectId("511232046ce24e7ea3269910"), "name" : "denis1969", "age" : 1969 }
{ "_id" : ObjectId("511232046ce24e7ea3269911"), "name" : "denis1970", "age" : 1970 }
{ "_id" : ObjectId("511232046ce24e7ea3269912"), "name" : "denis1971", "age" : 1971 }
{ "_id" : ObjectId("511232046ce24e7ea3269913"), "name" : "denis1972", "age" : 1972 }
{ "_id" : ObjectId("511232046ce24e7ea3269914"), "name" : "denis1973", "age" : 1973 }
{ "_id" : ObjectId("511232046ce24e7ea3269915"), "name" : "denis1974", "age" : 1974 }
{ "_id" : ObjectId("511232046ce24e7ea3269916"), "name" : "denis1975", "age" : 1975 }
{ "_id" : ObjectId("511232046ce24e7ea3269917"), "name" : "denis1976", "age" : 1976 }
{ "_id" : ObjectId("511232046ce24e7ea3269918"), "name" : "denis1977", "age" : 1977 }
{ "_id" : ObjectId("511232046ce24e7ea3269919"), "name" : "denis1978", "age" : 1978 }
{ "_id" : ObjectId("511232046ce24e7ea326991a"), "name" : "denis1979", "age" : 1979 }
{ "_id" : ObjectId("511232046ce24e7ea326991b"), "name" : "denis1980", "age" : 1980 }
{ "_id" : ObjectId("511232046ce24e7ea326991c"), "name" : "denis1981", "age" : 1981 }
{ "_id" : ObjectId("511232046ce24e7ea326991d"), "name" : "denis1982", "age" : 1982 }
{ "_id" : ObjectId("511232046ce24e7ea326991e"), "name" : "denis1983", "age" : 1983 }
{ "_id" : ObjectId("511232046ce24e7ea326991f"), "name" : "denis1984", "age" : 1984 }
{ "_id" : ObjectId("511232046ce24e7ea3269920"), "name" : "denis1985", "age" : 1985 }
{ "_id" : ObjectId("511232046ce24e7ea3269921"), "name" : "denis1986", "age" : 1986 }
{ "_id" : ObjectId("511232046ce24e7ea3269922"), "name" : "denis1987", "age" : 1987 }
Type "it" for more
> it
{ "_id" : ObjectId("511232046ce24e7ea3269923"), "name" : "denis1988", "age" : 1988 }
{ "_id" : ObjectId("511232046ce24e7ea3269924"), "name" : "denis1989", "age" : 1989 }
{ "_id" : ObjectId("511232046ce24e7ea3269925"), "name" : "denis1990", "age" : 1990 }
{ "_id" : ObjectId("511232046ce24e7ea3269926"), "name" : "denis1991", "age" : 1991 }
{ "_id" : ObjectId("511232046ce24e7ea3269927"), "name" : "denis1992", "age" : 1992 }
{ "_id" : ObjectId("511232046ce24e7ea3269928"), "name" : "denis1993", "age" : 1993 }
{ "_id" : ObjectId("511232046ce24e7ea3269929"), "name" : "denis1994", "age" : 1994 }
{ "_id" : ObjectId("511232046ce24e7ea326992a"), "name" : "denis1995", "age" : 1995 }
{ "_id" : ObjectId("511232046ce24e7ea326992b"), "name" : "denis1996", "age" : 1996 }
{ "_id" : ObjectId("511232046ce24e7ea326992c"), "name" : "denis1997", "age" : 1997 }
{ "_id" : ObjectId("511232046ce24e7ea326992d"), "name" : "denis1998", "age" : 1998 }
{ "_id" : ObjectId("511232046ce24e7ea326992e"), "name" : "denis1999", "age" : 1999 }
{ "_id" : ObjectId("511232046ce24e7ea326992f"), "name" : "denis2000", "age" : 2000 }</pre>

If we do a count on the collection, we can find out how many items there are

<pre>db.TestSize.count()</pre>

That returns 53 for me

Let's add another item

<pre>db.TestSize.insert( { name : "denis2001" , age : 2001 } )</pre>

If we check the count again

<pre>db.TestSize.count()</pre>

That still returns 53. Before, the first item returned was the item with age of 1948, now that we added one more item, one should have been removed as well, we should see the item with age 1949 as the first item now.

<pre>db.TestSize.find()</pre>

Here is the output

<pre>{ "_id" : ObjectId("511232046ce24e7ea32698fc"), "name" : "denis1949", "age" : 1949 }
{ "_id" : ObjectId("511232046ce24e7ea32698fd"), "name" : "denis1950", "age" : 1950 }
{ "_id" : ObjectId("511232046ce24e7ea32698fe"), "name" : "denis1951", "age" : 1951 }
{ "_id" : ObjectId("511232046ce24e7ea32698ff"), "name" : "denis1952", "age" : 1952 }
{ "_id" : ObjectId("511232046ce24e7ea3269900"), "name" : "denis1953", "age" : 1953 }
{ "_id" : ObjectId("511232046ce24e7ea3269901"), "name" : "denis1954", "age" : 1954 }
{ "_id" : ObjectId("511232046ce24e7ea3269902"), "name" : "denis1955", "age" : 1955 }
{ "_id" : ObjectId("511232046ce24e7ea3269903"), "name" : "denis1956", "age" : 1956 }
{ "_id" : ObjectId("511232046ce24e7ea3269904"), "name" : "denis1957", "age" : 1957 }
{ "_id" : ObjectId("511232046ce24e7ea3269905"), "name" : "denis1958", "age" : 1958 }
{ "_id" : ObjectId("511232046ce24e7ea3269906"), "name" : "denis1959", "age" : 1959 }
{ "_id" : ObjectId("511232046ce24e7ea3269907"), "name" : "denis1960", "age" : 1960 }
{ "_id" : ObjectId("511232046ce24e7ea3269908"), "name" : "denis1961", "age" : 1961 }
{ "_id" : ObjectId("511232046ce24e7ea3269909"), "name" : "denis1962", "age" : 1962 }
{ "_id" : ObjectId("511232046ce24e7ea326990a"), "name" : "denis1963", "age" : 1963 }
{ "_id" : ObjectId("511232046ce24e7ea326990b"), "name" : "denis1964", "age" : 1964 }
{ "_id" : ObjectId("511232046ce24e7ea326990c"), "name" : "denis1965", "age" : 1965 }
{ "_id" : ObjectId("511232046ce24e7ea326990d"), "name" : "denis1966", "age" : 1966 }
{ "_id" : ObjectId("511232046ce24e7ea326990e"), "name" : "denis1967", "age" : 1967 }
{ "_id" : ObjectId("511232046ce24e7ea326990f"), "name" : "denis1968", "age" : 1968 }
Type "it" for more
> it
{ "_id" : ObjectId("511232046ce24e7ea3269910"), "name" : "denis1969", "age" : 1969 }
{ "_id" : ObjectId("511232046ce24e7ea3269911"), "name" : "denis1970", "age" : 1970 }
{ "_id" : ObjectId("511232046ce24e7ea3269912"), "name" : "denis1971", "age" : 1971 }
{ "_id" : ObjectId("511232046ce24e7ea3269913"), "name" : "denis1972", "age" : 1972 }
{ "_id" : ObjectId("511232046ce24e7ea3269914"), "name" : "denis1973", "age" : 1973 }
{ "_id" : ObjectId("511232046ce24e7ea3269915"), "name" : "denis1974", "age" : 1974 }
{ "_id" : ObjectId("511232046ce24e7ea3269916"), "name" : "denis1975", "age" : 1975 }
{ "_id" : ObjectId("511232046ce24e7ea3269917"), "name" : "denis1976", "age" : 1976 }
{ "_id" : ObjectId("511232046ce24e7ea3269918"), "name" : "denis1977", "age" : 1977 }
{ "_id" : ObjectId("511232046ce24e7ea3269919"), "name" : "denis1978", "age" : 1978 }
{ "_id" : ObjectId("511232046ce24e7ea326991a"), "name" : "denis1979", "age" : 1979 }
{ "_id" : ObjectId("511232046ce24e7ea326991b"), "name" : "denis1980", "age" : 1980 }
{ "_id" : ObjectId("511232046ce24e7ea326991c"), "name" : "denis1981", "age" : 1981 }
{ "_id" : ObjectId("511232046ce24e7ea326991d"), "name" : "denis1982", "age" : 1982 }
{ "_id" : ObjectId("511232046ce24e7ea326991e"), "name" : "denis1983", "age" : 1983 }
{ "_id" : ObjectId("511232046ce24e7ea326991f"), "name" : "denis1984", "age" : 1984 }
{ "_id" : ObjectId("511232046ce24e7ea3269920"), "name" : "denis1985", "age" : 1985 }
{ "_id" : ObjectId("511232046ce24e7ea3269921"), "name" : "denis1986", "age" : 1986 }
{ "_id" : ObjectId("511232046ce24e7ea3269922"), "name" : "denis1987", "age" : 1987 }
{ "_id" : ObjectId("511232046ce24e7ea3269923"), "name" : "denis1988", "age" : 1988 }
Type "it" for more
> it
{ "_id" : ObjectId("511232046ce24e7ea3269924"), "name" : "denis1989", "age" : 1989 }
{ "_id" : ObjectId("511232046ce24e7ea3269925"), "name" : "denis1990", "age" : 1990 }
{ "_id" : ObjectId("511232046ce24e7ea3269926"), "name" : "denis1991", "age" : 1991 }
{ "_id" : ObjectId("511232046ce24e7ea3269927"), "name" : "denis1992", "age" : 1992 }
{ "_id" : ObjectId("511232046ce24e7ea3269928"), "name" : "denis1993", "age" : 1993 }
{ "_id" : ObjectId("511232046ce24e7ea3269929"), "name" : "denis1994", "age" : 1994 }
{ "_id" : ObjectId("511232046ce24e7ea326992a"), "name" : "denis1995", "age" : 1995 }
{ "_id" : ObjectId("511232046ce24e7ea326992b"), "name" : "denis1996", "age" : 1996 }
{ "_id" : ObjectId("511232046ce24e7ea326992c"), "name" : "denis1997", "age" : 1997 }
{ "_id" : ObjectId("511232046ce24e7ea326992d"), "name" : "denis1998", "age" : 1998 }
{ "_id" : ObjectId("511232046ce24e7ea326992e"), "name" : "denis1999", "age" : 1999 }
{ "_id" : ObjectId("511232046ce24e7ea326992f"), "name" : "denis2000", "age" : 2000 }
{ "_id" : ObjectId("511233896ce24e7ea3269930"), "name" : "denis2001", "age" : 2001 }</pre>

And as expected the item with age 1949 was indeed the first item, the item we just inserted is now the last item

You can check if a collection is capped by using `db.Collection.isCapped()`. In our case the command would be

<pre>db.TestSize.isCapped()</pre>

The output will be `true` if the collection is capped.

**Restrictions**
  
Here are some restrictions that you have to be aware of

> You can update documents in a collection after inserting them; however, these updates cannot cause the documents to grow. If the update operation causes the document to grow beyond their original size, the update operation will fail.
> 
> If you plan to update documents in a capped collection, remember to create an index to prevent update operations that require a table scan.
> 
> You cannot delete documents from a capped collection. To remove all records from a capped collection, use the ‘emptycapped’ command. To remove the collection entirely, use the drop() method. 

You can use capped collections if you are only interested in the last x number of items inserted, for example you want to show only the last 50 twitter messages or the last 100 trades for a particular stock.

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