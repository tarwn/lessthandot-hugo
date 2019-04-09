---
title: 'MongoDB: Exporting data into files'
author: SQLDenis
type: post
date: 2013-02-03T11:25:00+00:00
ID: 1960
excerpt: |
  In this post we are going to take a look at how to export data into files. We are going to export data in json format as well as in csv format. To get started first connect to mongodb and create a new database named ExportDB
  
  You can just execute the&hellip;
url: /index.php/datamgmt/dbprogramming/mongodb-exporting-data-into-files/
views:
  - 13861
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
tags:
  - bigdata
  - collections
  - export
  - mongodb
  - nosql

---
In this post we are going to take a look at how to export data into files. We are going to export data in json format as well as in csv format. To get started first connect to mongodb and create a new database named ExportDB

You can just execute the following to create this database

<pre>use ExportDB</pre>

You should get a message like the following

<pre>switched to db ExportDb</pre>

Insert these 5 items

<pre>db.Person.insert( { name : "Denis",  age : 20, city : "Princeton" } )
db.Person.insert( { name : "Abe",    age : 30, city : "Amsterdam" } )
db.Person.insert( { name : "John",   age : 40, city : "New York"  } )
db.Person.insert( { name : "Xavier", age : 10, city : "Barcelona" } )
db.Person.insert( { name : "Zen",    age : 50, city : "Kyoto"     } )</pre>

Now it is time to do the export. To do the export we will use the mongoexport utility, you can find this in the bin directory of your MongoDB install. I my case it is in the C:NoSQLmongodbbin directory. Open up a new command window and cd into the folder where mongoexport is located.

## Export in json format

To export the data, we need to tell mongoexport what database to use, what collection to use and optionally we also need to specify what fields to use. If you don't specify the fields, you will get all of them. If we wanted to export the name and age fields from the Person collection in the ExportDb database, you would specify it like this

<pre>mongoexport --db ExportDb --collection Person -fields name,age</pre>

Here is the output

<pre>C:NoSQLmongodbbin>mongoexport --db ExportDb --collection Person -fields name,age
connected to: 127.0.0.1
{ "_id" : { "$oid" : "510e5da10e0a53ddf5f2865b" }, "name" : "Denis", "age" : 20 }
{ "_id" : { "$oid" : "510e5da20e0a53ddf5f2865c" }, "name" : "Abe", "age" : 30 }
{ "_id" : { "$oid" : "510e5da20e0a53ddf5f2865d" }, "name" : "John", "age" : 40 }
{ "_id" : { "$oid" : "510e5da20e0a53ddf5f2865e" }, "name" : "Xavier", "age" : 10 }
{ "_id" : { "$oid" : "510e5da80e0a53ddf5f2865f" }, "name" : "Zen", "age" : 50 }
exported 5 records</pre>

As you can see you got the output in the window. What about a file? You can use -o with the filename to redirect output into a file, all we are adding is -o Person.txt to the previous command

<pre>mongoexport --db ExportDb --collection Person  -fields name,age -o Person.txt</pre>

Here is the output

<pre>C:NoSQLmongodbbin>mongoexport --db ExportDb --collection Person  -fields name,age -o Person.txt
connected to: 127.0.0.1
exported 5 records

C:NoSQLmongodbbin>
</pre>

If you now locate the file in the bin directory and open the file, you should see something like this

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/mongo/PersonOutput.PNG?mtime=1359896992"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/mongo/PersonOutput.PNG?mtime=1359896992" width="660" height="184" /></a>
</div>

## Export in csv format

Exporting in csv format is pretty much the same as before with the addition of &#8211;csv. Our command will look like this

<pre>mongoexport --db ExportDb --collection Person --csv -fields name,age</pre>

Here is the output

<pre>C:NoSQLmongodbbin>mongoexport --db ExportDb --collection Person --csv -fields name,age
connected to: 127.0.0.1
name,age
"Denis",20.0
"Abe",30.0
"John",40.0
"Xavier",10.0
"Zen",50.0
exported 5 records

C:NoSQLmongodbbin></pre>

Just like before we want to direct the output into a file instead of onto the screen, we will just add -o Person.csv to the command , the command will look like this

mongoexport &#8211;db ExportDb &#8211;collection Person &#8211;csv -fields name,age -o Person.csv

<pre>C:NoSQLmongodbbin>mongoexport --db ExportDb --collection Person --csv -fields name,age -o Person.csv
connected to: 127.0.0.1
exported 5 records

C:NoSQLmongodbbin></pre>

Now if we open up the file in notepad you will see the following

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/mongo/PersonOutputCsv.PNG?mtime=1359897545"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/mongo/PersonOutputCsv.PNG?mtime=1359897545" width="289" height="181" /></a>
</div>

If you open up the file in Excel, it will look like this

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/mongo/PersonOutputExcel.PNG?mtime=1359897556"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/mongo/PersonOutputExcel.PNG?mtime=1359897556" width="498" height="362" /></a>
</div>

There you have it, if you want to quickly export some data into file, you can use the mongoexport utility to accomplish that

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