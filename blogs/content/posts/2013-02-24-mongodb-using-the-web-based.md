---
title: 'MongoDB: Using the web-based administrative tool'
author: SQLDenis
type: post
date: 2013-02-24T13:14:00+00:00
ID: 2010
excerpt: |
  MongoDB ships with a web-based administrative tool. You can see this tool by going to http://localhost:28017/
  
  You can see a bunch of stuff on this screen.
  
  However when trying any of these on top
  
  List all commands | Replica set status
  
  Command&hellip;
url: /index.php/datamgmt/dbprogramming/mongodb-using-the-web-based/
views:
  - 9170
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
tags:
  - administration
  - bigdata
  - mongodb
  - nosql
  - tools

---
MongoDB ships with a web-based administrative tool. You can see this tool by going to http://localhost:28017/

You can see a bunch of stuff on the administrative tool screen. here is a partial image.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/mongo/MongoDbAdminTool.PNG?mtime=1361716184"><img alt="MongoDB: web-based administrative tool" src="/wp-content/uploads/blogs/DataMgmt/Denis/mongo/MongoDbAdminTool.PNG?mtime=1361716184" width="823" height="573" title="MongoDB: web-based administrative tool" /></a>
</div>

There are a bunch of links that you can click as well. If you click on any of the links on top

`List all commands | Replica set status</p>
<p>Commands: buildInfo cursorInfo features hostInfo isMaster listDatabases replSetGetStatus serverStatus top`

You get the following message 

<pre>REST is not enabled.  use --rest to turn on.
check that port 28017 is secured for the network too.</pre>

So here is how you can fix that, open the config file mongod.cfg

In my case, I have the following there, yours will look different

<pre>logpath=C:NoSQLmongodblogmongo.log 
dbpath=C:NoSQLmongodbdata</pre>

Add the line `rest=true` at the bottom, so that it becomes

<pre>logpath=C:NoSQLmongodblogmongo.log 
dbpath=C:NoSQLmongodbdata
rest=true</pre>

Here is what it looks like when opened in notepad.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/mongo/MongoDBCfg.PNG?mtime=1361716194"><img alt="mongod.cfg" title ="mongod.cfg"  src="/wp-content/uploads/blogs/DataMgmt/Denis/mongo/MongoDBCfg.PNG?mtime=1361716194" width="435" height="168" /></a>
</div>

Save it, restart the MongoDB service. If you want to learn how to have MongoDB run as a service on Windows, take a look at [Creating MongoDB as a service on Windows 8][1], the process should be the same for Windows 7

If you click on [List all commands][2] you will see a list of all the commands. Here is an image of a partial list

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/mongo/MongoDbAdminToolCommandList.PNG?mtime=1361716729"><img alt="MongoDB: List all commands" title ="MongoDB: List all commands" src="/wp-content/uploads/blogs/DataMgmt/Denis/mongo/MongoDbAdminToolCommandList.PNG?mtime=1361716729" width="675" height="397" /></a>
</div>

Scroll down to hostinfo and click on it you will see the following output

<pre>{ "system" : { "currentTime" : { "$date" : "Sun Feb 24 09:43:31 2013" },
    "hostname" : "Denis",
    "cpuAddrSize" : 64,
    "memSizeMB" : 4095,
    "numCores" : 2,
    "cpuArch" : "x86_64",
    "numaEnabled" : false },
  "os" : { "type" : "Windows",
    "name" : "Microsoft Windows 8",
    "version" : "6.2 (build 9200)" },
  "extra" : { "pageSize" : 4096 } }</pre>

You can now bookmark some of the most used commands, hostinfo for example is http://localhost:28017/hostInfo?text=1

Go ahead and play around with the tool, check out all the links and also explore some of the commands that are listed

That is all for this post, if you are interested in my other MongoDB posts, you can find them here:
  
[Install MongoDB as a Windows Service][1]
  
[UPSERTs with MongoDB][3]
  
[How to sort results in MongoDB][4]
  
[Indexes in MongoDB: A quick overview][5]
  
[Multidocument updates with MongoDB][6]
  
[MongoDB: How to include and exclude the fields you want in results][7]
  
[MongoDB: How to limit results and how to page through results][8]
  
[MongoDB: How to backup and restore databases][9]
  
[MongoDB: How to restore collections][10]
  
[MongoDB: How to backup all the databases with one command][11]
  
[MongoDB: Exporting data into files][12]
  
[MongoDB: How to drop databases and collections][13]
  
[MongoDB: Creating capped collections][14]
  
[MongoDB: Returning documents where fields are null or not existing][15]

 [1]: /index.php/DataMgmt/DBProgramming/creating-mongodb-as-a-service
 [2]: http://localhost:28017/_commands
 [3]: /index.php/DataMgmt/DBProgramming/doing-upserts-in-mongodb
 [4]: /index.php/DataMgmt/DBProgramming/mongodb-how-to-sort-results
 [5]: /index.php/DataMgmt/DBProgramming/indexes-in-mongodb
 [6]: /index.php/DataMgmt/DBProgramming/multidocument-updates-with-mongodb
 [7]: /index.php/DataMgmt/DBProgramming/mongodb-how-to-include-and
 [8]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/mongodb-how-to-limit-results
 [9]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/mongodb-backup-and-restore-databases
 [10]: /index.php/DataMgmt/DBAdmin/mongodb-how-to-restore-collections
 [11]: /index.php/DataMgmt/DBAdmin/mongodb-how-to-backup-all
 [12]: /index.php/DataMgmt/DBProgramming/mongodb-exporting-data-into-files
 [13]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/mongodb-how-to-drop-databases
 [14]: /index.php/DataMgmt/DBProgramming/mongodb-creating-capped-collections
 [15]: /index.php/DataMgmt/DBProgramming/mongodb-returning-documents-where-fields