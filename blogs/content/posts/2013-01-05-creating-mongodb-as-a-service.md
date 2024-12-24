---
title: Creating MongoDB as a service on Windows 8
author: SQLDenis
type: post
date: 2013-01-05T16:14:00+00:00
ID: 1898
excerpt: |
  In addition to Scala I decided to mess around with MongoDB as well. This post is about how to install MongoDB as a service on Windows 8. 
  
  Here is what MongoDB is about according to their site
  
  MongoDB (from "humongous") is a scalable, high-performa&hellip;
url: /index.php/datamgmt/dbprogramming/creating-mongodb-as-a-service/
views:
  - 13400
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - Database Programming
tags:
  - bigdata
  - mongodb
  - nosql

---
In addition to Scala I decided to mess around with MongoDB as well. This post is about how to install MongoDB as a service on Windows 8. BTW this should also work exactly the same on Windows 7

Here is what wikipedia has to say about MongoDB

> MongoDB (from "humongous") is an open source document-oriented database system developed and supported by 10gen. It is part of the NoSQL family of database systems. Instead of storing data in tables as is done in a "classical" relational database, MongoDB stores structured data as JSON-like documents with dynamic schemas (MongoDB calls the format BSON), making the integration of data in certain types of applications easier and faster.

Here is what MongoDB is about according to their site

> MongoDB (from "hu**mongo**us") is a scalable, high-performance, open source NoSQL database. Written in C++, MongoDB features:
> 
> Document-Oriented Storage »
  
> JSON-style documents with dynamic schemas offer simplicity and power.
> 
> Full Index Support »
  
> Index on any attribute, just like you're used to.
> 
> Replication & High Availability »
  
> Mirror across LANs and WANs for scale and peace of mind.
> 
> Auto-Sharding »
  
> Scale horizontally without compromising functionality.
> 
> Querying »
  
> Rich, document-based queries.
> 
> Fast In-Place Updates »
  
> Atomic modifiers for contention-free performance.
> 
> Map/Reduce »
  
> Flexible aggregation and data processing.
> 
> GridFS »
  
> Store files of any size without complicating your stack.

To get started download MongoDB here: http://www.mongodb.org/downloads

If you are running the 64 bit version of Windows 7,8 or Windows Server 2008 R2 and higher then choose the *2008R2+ link

Once you have MongoDB downloaded it is time to extract it. I created a NoSQL folder on my C Drive, here is where MongoDB and other NoSQL databases will live. Extract the zipfile into the C:|NoSQL folder and rename the folder to mongodb. In the mongodb folder you should have the following

bin
  
GNU-AGPL-3.0
  
README
  
THIRD-PARTY-NOTICES

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/Denis/mongo/mongo2.PNG?mtime=1357406776"><img alt="MongoDB file folder contents" title ="MongoDB file folder contents"  src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/Denis/mongo/mongo2.PNG?mtime=1357406776" width="455" height="95" /></a>
</div>

After you are done with that you need to create a data folder, inside that folder you need a db folder. Your db folder would be in this location C:NoSQLmongodbdatadb

Let's see if we can get MongoDB to start up.
  
Open up a Command Prompt (WinKey + X + C) and paste this in
  
C:NoSQLmongodbbinmongod.exe –dbpath C:NoSQLmongodbdata

Running that should trigger a firewall warning

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/Denis/mongo/Mongo1.PNG?mtime=1357406796"><img alt="MongoDB firewall warning"  title="MongoDB firewall warning" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/Denis/mongo/Mongo1.PNG?mtime=1357406796" width="526" height="373" /></a>
</div>

Check Private Networks and click Allow Access

Your Command Prompt window should have the following

_C:UsersDenis>C:NoSQLmongodbbinmongod.exe –dbpath C:NoSQLmongodbdata
  
Sat Jan 05 12:12:25 [initandlisten] MongoDB starting : pid=5780 port=27017 dbpat
  
h=C:NoSQLmongodbdata 64-bit host=Denis
  
Sat Jan 05 12:12:25 [initandlisten] db version v2.2.2, pdfile version 4.5
  
Sat Jan 05 12:12:25 [initandlisten] git version: d1b43b61a5308c4ad0679d34b262c5a
  
f9d664267
  
Sat Jan 05 12:12:25 [initandlisten] build info: windows sys.getwindowsversion(ma
  
jor=6, minor=1, build=7601, platform=2, service\_pack='Service Pack 1') BOOST\_LIB
  
\_VERSION=1\_49
  
Sat Jan 05 12:12:25 [initandlisten] options: { dbpath: "C:NoSQLmongodbdata" }</p> 

Sat Jan 05 12:12:25 [initandlisten] journal dir=C:/NoSQL/mongodb/data/journal
  
Sat Jan 05 12:12:25 [initandlisten] recover : no journal files present, no recov
  
ery needed
  
Sat Jan 05 12:12:26 [initandlisten] waiting for connections on port 27017
  
Sat Jan 05 12:12:26 [websvr] admin web console waiting for connections on port 2
  
8017</em>

As you can see it is working, hit CTRL + C inside the Command Prompt to stop MongoDB, you should see the following

_Sat Jan 05 12:13:24 Ctrl-C signal
  
Sat Jan 05 12:13:24 [consoleTerminate] got CTRL\_C\_EVENT, will terminate after cu
  
rrent cmd ends
  
Sat Jan 05 12:13:24 [consoleTerminate] now exiting
  
Sat Jan 05 12:13:24 dbexit:
  
Sat Jan 05 12:13:24 [consoleTerminate] shutdown: going to close listening socket
  
s...
  
Sat Jan 05 12:13:24 [consoleTerminate] closing listening socket: 352
  
Sat Jan 05 12:13:24 [consoleTerminate] closing listening socket: 372
  
Sat Jan 05 12:13:24 [consoleTerminate] shutdown: going to flush diaglog...
  
Sat Jan 05 12:13:24 [consoleTerminate] shutdown: going to close sockets...
  
Sat Jan 05 12:13:24 [consoleTerminate] shutdown: waiting for fs preallocator...
  
Sat Jan 05 12:13:24 [consoleTerminate] shutdown: lock for final commit...
  
Sat Jan 05 12:13:24 [consoleTerminate] shutdown: final commit...
  
Sat Jan 05 12:13:24 [consoleTerminate] shutdown: closing all files...
  
Sat Jan 05 12:13:24 [consoleTerminate] closeAllFiles() finished
  
Sat Jan 05 12:13:24 [consoleTerminate] journalCleanup...
  
Sat Jan 05 12:13:24 [consoleTerminate] removeJournalFiles
  
Sat Jan 05 12:13:24 [consoleTerminate] shutdown: removing fs lock...
  
Sat Jan 05 12:13:24 dbexit: really exiting now_

Creating the
  


# MongoDB Windows Service

Now it is time to create the MongoDB Windows Service. In the C:NoSQLmongodb folder create a log folder.

Run the following in a Command Prompt

_echo logpath=C:NoSQLmongodblogmongo.log > C:NoSQLmongodbmongod.cfg_

Open up the config file C:NoSQLmongodbmongod.cfg and add this line
  
dbpath=C:NoSQLmongodbdata

We need to add this line because by default mongodb will look in C:data

The file should have two lines in it, it should look like this
  
logpath=C:NoSQLmongodblogmongo.log
  
dbpath=C:NoSQLmongodbdata

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/Denis/mongo/mongo4.PNG?mtime=1357409078"><img alt="MongoDB configuration file"  title="MongoDB configuration file" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/Denis/mongo/mongo4.PNG?mtime=1357409078" width="443" height="208" /></a>
</div>

In a Command Prompt with elevated privileges (Winkey + X + A) run the following

_C:NoSQLmongodbbinmongod.exe –config C:NoSQLmongodbmongod.cfg –install_

Now run the following command

_net start mongodb_

You should get back the following message

_The Mongo DB service was started successfully._

If you look at the services (either click on Services from Control PanelSystem and SecurityAdministrative Tools or type services from the start screen), you should see the following

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/Denis/mongo/Mongo3.PNG?mtime=1357408031"><img alt="MongoDB running as a windows 8 service"  title="MongoDB running as a windows 8 service" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/Denis/mongo/Mongo3.PNG?mtime=1357408031" width="623" height="44" /></a>
</div>

Congratulations, the service is now running

Let's take it for a test, open up a Command Prompt and paste the following _C:NoSQLmongodbbinmongo_

You should see
  
_MongoDB shell version: 2.2.2
  
connecting to: test
  
Welcome to the MongoDB shell.
  
For interactive help, type "help".
  
For more comprehensive documentation, see
          
http://docs.mongodb.org/
  
Questions? Try the support group
          
http://groups.google.com/group/mongodb-user_

Type **db** and hit enter, you should see the following
  
test

Type **show dbs** and hit enter, you should see the following
  
local (empty)

Type **use test** and hit enter, you should see the following
  
switched to db test

Type **k = { x : 5 }** and hit enter, you should see the following
  
{ "x" : 5 }

Type **j = { name : "Denis"}** and hit enter, you should see the following
  
{ "name" : "Denis" }

Now let's insert j and k into the database
  
Type **db.things.insert( j )** and hit enter
  
Type **db.things.insert( k )** and hit enter

Now let's see what we inserted

Type **show collections** and hit enter, you should see the following
  
system.indexes
  
things

Type **db.things.find()** and hit enter, you should see the following
  
{ "_id" : ObjectId("50e861b1065e4ad1d2279af6"), "name" : "Denis" }
  
{ "_id" : ObjectId("50e861b9065e4ad1d2279af7"), "x" : 5 }

Let's use a loop to insert another 9 items
  
Type **for (var i = 1; i <= 9; i++) db.things.insert( { x : 5 , j : i } )** and hit enter

Type **db.things.find()** and hit enter, you should see the following
  
{ "_id" : ObjectId("50e861b1065e4ad1d2279af6"), "name" : "Denis" }
  
{ "_id" : ObjectId("50e861b9065e4ad1d2279af7"), "x" : 5 }
  
{ "_id" : ObjectId("50e86200065e4ad1d2279af8"), "x" : 5, "j" : 1 }
  
{ "_id" : ObjectId("50e86200065e4ad1d2279af9"), "x" : 5, "j" : 2 }
  
{ "_id" : ObjectId("50e86200065e4ad1d2279afa"), "x" : 5, "j" : 3 }
  
{ "_id" : ObjectId("50e86200065e4ad1d2279afb"), "x" : 5, "j" : 4 }
  
{ "_id" : ObjectId("50e86200065e4ad1d2279afc"), "x" : 5, "j" : 5 }
  
{ "_id" : ObjectId("50e86200065e4ad1d2279afd"), "x" : 5, "j" : 6 }
  
{ "_id" : ObjectId("50e86200065e4ad1d2279afe"), "x" : 5, "j" : 7 }
  
{ "_id" : ObjectId("50e86200065e4ad1d2279aff"), "x" : 5, "j" : 8 }
  
{ "_id" : ObjectId("50e86200065e4ad1d2279b00"), "x" : 5, "j" : 9 }

This post was mostly to show you how you can setup MongoDB to run as a service on Windows 8. In subsequent posts we will take a look at how to do more interesting stuff with MongoDB. Stay tuned......