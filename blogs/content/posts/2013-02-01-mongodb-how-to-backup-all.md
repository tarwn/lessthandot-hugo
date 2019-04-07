---
title: 'MongoDB: How to backup all the databases with one command'
author: SQLDenis
type: post
date: 2013-02-01T08:48:00+00:00
ID: 1955
excerpt: 'We looked at how to backup and restore databases in the post MongoDB: How to backup and restore databases. We also looked at how to restore collection in the post MongoDB: How to restore collections. Today we are going to look at how to backup all the d&hellip;'
url: /index.php/datamgmt/dbadmin/mongodb-how-to-backup-all/
views:
  - 14054
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
We looked at how to backup and restore databases in the post [MongoDB: How to backup and restore databases][1]. We also looked at how to restore collection in the post [MongoDB: How to restore collections][2]. Today we are going to look at how to backup all the databases in one shot with one simple command. Before we get started connect to your MongoDB server, we are going to create a couple of databases

Run the following

<pre>use MultiCollection

db.Blog.insert( { name : "Denis",  age : 20 } )
db.Blog.insert( { name : "Abe",    age : 30 } )
db.Blog.insert( { name : "John",   age : 40 } )
db.Blog.insert( { name : "Xavier", age : 10 } )
db.Blog.insert( { name : "Zen",    age : 50 } )


db.People.insert( { name : "AADenis",  age : 0020 } )
db.People.insert( { name : "AAAbe",    age : 0030 } )
db.People.insert( { name : "AAJohn",   age : 0040 } )
db.People.insert( { name : "AAXavier", age : 0010 } )
db.People.insert( { name : "AAZen",    age : 0050 } )


use SingleCollection

db.Blog.insert( { name : "Denis",  age : 20 } )
db.Blog.insert( { name : "Abe",    age : 30 } )
db.Blog.insert( { name : "John",   age : 40 } )
db.Blog.insert( { name : "Xavier", age : 10 } )
db.Blog.insert( { name : "Zen",    age : 50 } )

use TestDB

db.Test.insert( { name : "Denis",  age : 20 } )
db.Test.insert( { name : "Abe",    age : 30 } )
db.Test.insert( { name : "John",   age : 40 } )
db.Test.insert( { name : "Xavier", age : 10 } )
db.Test.insert( { name : "Zen",    age : 50 } )

use TestStuff

db.Stuff.insert( { name : "Denis",  age : 20 } )
db.Stuff.insert( { name : "Abe",    age : 30 } )
db.Stuff.insert( { name : "John",   age : 40 } )
db.Stuff.insert( { name : "Xavier", age : 10 } )
db.Stuff.insert( { name : "Zen",    age : 50 } )
</pre>

The code above will create 4 databases. Now it is time to back all the databases up. Open up another command window, navigate to the bin directory where MongoDB is installed

You can of course run the mongodump command for every database you have, for example

<pre>mongodump --db MultiCollection
mongodump --db SingleCollection
mongodump --db TestDB
mongodump --db TestStuff</pre>

But did you know that if you don&#8217;t specify a database that mongodump will back up all the databases? Try it out, run the following

mongodump 

Here is what the output looks like

<pre>C:NoSQLmongodbbin>mongodump
connected to: 127.0.0.1
Fri Feb 01 05:41:33 all dbs
Fri Feb 01 05:41:35 DATABASE: MultiCollection    to     dump/MultiCollection
Fri Feb 01 05:41:35     MultiCollection.Blog to dump/MultiCollection/Blog.bson
Fri Feb 01 05:41:35              5 objects
Fri Feb 01 05:41:36     Metadata for MultiCollection.Blog to dump/MultiCollection/Blog.metadata.json
Fri Feb 01 05:41:36     MultiCollection.People to dump/MultiCollection/People.bson
Fri Feb 01 05:41:36              5 objects
Fri Feb 01 05:41:36 Metadata for MultiCollection.People to dump/MultiCollection/People.metadata.json
Fri Feb 01 05:41:36 DATABASE: SingleCollection   to     dump/SingleCollection
Fri Feb 01 05:41:36     SingleCollection.Blog to dump/SingleCollection/Blog.bson
Fri Feb 01 05:41:36              5 objects
Fri Feb 01 05:41:36     Metadata for SingleCollection.Blog to dump/SingleCollection/Blog.metadata.json
Fri Feb 01 05:41:36 DATABASE: TestDB     to     dump/TestDB
Fri Feb 01 05:41:36     TestDB.Test to dump/TestDB/Test.bson
Fri Feb 01 05:41:36              5 objects
Fri Feb 01 05:41:36     Metadata for TestDB.Test to dump/TestDB/Test.metadata.json
Fri Feb 01 05:41:36 DATABASE: TestStuff  to     dump/TestStuff
Fri Feb 01 05:41:36     TestStuff.Stuff to dump/TestStuff/Stuff.bson
Fri Feb 01 05:41:36              5 objects
Fri Feb 01 05:41:36     Metadata for TestStuff.Stuff to dump/TestStuff/Stuff.metadata.json
Fri Feb 01 05:41:36 DATABASE: admin      to     dump/admin

C:NoSQLmongodbbin></pre>

If you look in your bin directory, you will see a dump directory, inside the dump directory, you will see a directory for every database that was backed up.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/MultiDBackupMongoDB.PNG?mtime=1359714862"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/MultiDBackupMongoDB.PNG?mtime=1359714862" width="640" height="265" /></a>
</div>

That is all for this post, if you are interested in my other MongoDB posts, you can find them here:
  
[Install MongoDB as a Windows Service][3]
  
[UPSERTs with MongoDB][4]
  
[How to sort results in MongoDB][5]
  
[Indexes in MongoDB: A quick overview][6]
  
[Multidocument updates with MongoDB][7]
  
[MongoDB: How to include and exclude the fields you want in results][8]
  
[MongoDB: How to limit results and how to page through results][9]
  
[MongoDB: How to backup and restore databases][1]
  
[MongoDB: How to restore collections][2]

 [1]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/mongodb-backup-and-restore-databases
 [2]: /index.php/DataMgmt/DBAdmin/mongodb-how-to-restore-collections
 [3]: /index.php/DataMgmt/DBProgramming/creating-mongodb-as-a-service
 [4]: /index.php/DataMgmt/DBProgramming/doing-upserts-in-mongodb
 [5]: /index.php/DataMgmt/DBProgramming/mongodb-how-to-sort-results
 [6]: /index.php/DataMgmt/DBProgramming/indexes-in-mongodb
 [7]: /index.php/DataMgmt/DBProgramming/multidocument-updates-with-mongodb
 [8]: /index.php/DataMgmt/DBProgramming/mongodb-how-to-include-and
 [9]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/mongodb-how-to-limit-results