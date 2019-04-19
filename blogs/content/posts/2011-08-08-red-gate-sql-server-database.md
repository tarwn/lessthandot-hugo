---
title: Red Gate SQL Toolbelt Review
author: David Forck (thirster42)
type: post
date: 2011-08-08T13:32:00+00:00
ID: 1292
excerpt: So I've had Redgate's SQL Toolbelt for about a year now, but up until recently I hadn't used most of the tools available to me.  But with the current push we're making with trying to straighten out our processes I've been playing with everything to get&hellip;
url: /index.php/datamgmt/dbadmin/red-gate-sql-server-database/
views:
  - 9956
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
So I've had Redgate's <a href="http://www.red-gate.com/products/sql-development/sql-toolbelt/" target="_new">SQL Toolbelt</a> for about a year now, but up until recently I hadn't used most of the tools available to me. But with the current push we're making with trying to straighten out our processes I've been playing with everything to get a feel for how they work and if I want to actually use them. So far I've been fairly impressed with every piece of software provided by Redgate. Here's my quick review of the SQL Toolbelt

**SQL Compare**
  
This is the tool that I primarily had my eyes on when I first started asking for the SQL Toolbelt, and I've loved it ever since. Before we got SQL Compare I was manually creating scripts to copy data down from prod/test, creating backups, then copying and restarting from the backups to push changes to test/prod. Now I just back up the database, do a sql compare, and run the script that's generated. Out of all of the tools, this is the one that I'd recommend the most.

**SQL Data Compare**
  
SQL Data Compare has saved me so much time since we've gotten it that it probably pays for the entire license for the SQL Toolbelt in a year. It allows me to compare the data between two databases on the same server or different servers, script the difference, and then copy and paste the script to the target. I now no longer have to manually create scripts to copy any data, and I no longer have to move around backups. This is a must if you prefer updating your test and dev data with what's in production.

**SQL Dependency Tracker**
  
SQL Dependency Track is a very interesting tool, and is a life saver on complicated databases, because when you need to make a change to a table you can find exactly which views, sps, etc. will be effected by the update. A great time saver and a great investment.

**SQL Comparison SDK**
  
Something that I haven't played with.

**SQL Packager**
  
Another tool that I haven't gotten to play with.

**SQL Prompt**
  
SQL Prompt is the one tool that I've had rounds with. It sometimes has a mind of its own, but once you get used to it it's a great utility to have when you're writing new scripts or modifying old ones. The fact that you can set up your own custom way of formatting sql and then just hit a keystroke to format what's on the screen is worth the price of admission (and pain), especially if you're working on old code that's not formatted at all.

**SQL Doc**
  
SQL Doc is a pretty neat tool. It allows you to connect to a database and it presents you with all of the objects in the database. You can then make remarks on different parts of the object, and it then saves those remarks in the database in extended properties. So what that means is that if you manage to lose your SQL Doc project, all you have to do is reconnect to the database with SQL Doc and voila! You've got all of your documentation back. The other neat part is that if you use SQL Compare the documentation gets pushed to your test and dev servers (assuming you do your documentation on your dev server first). It's a great tool for anyone that has to do any sort of database validation, or just needs to document their database.

**SQL Backup**
  
I have all of my backups scheduled in SQL Server via maintenance plans and jobs. I've got several servers that I'm backing up. This tool allows me to add of my servers and track the backups. The only gripe I've got is that I can't zoom out past a couple of days to see the whole week. Great tool for those managing backups on multiple servers.

**SQL Monitor**
  
I got to play with SQL Monitor for a while with it installed on my local machine, and it was pretty awesome. I had so much data on my servers that I literally didn't know where to start. I was able to quickly figure everything out though, and it even helped me avert a couple of crisis. Sadly I haven't been able to get licenses to let me monitor every server I have. I highly recommend this for any DBA, or any dev house that doesn't have a dedicated DBA.

**SQL Multi Script**
  
I understand the use of this tool, but I haven't really had a need for it, so it's gone overlooked for now.

**SQL Data Generator**
  
This is another tool that I haven't really had a need to use. With the databases/applications that we work on we typically have a source of data that's going into the database. I look forward to testing this tool though when the time comes for it.

**SQL Object Level Recovery Native**
  
I haven't gotten around to playing with this tool.

**SQL Source Control**
  
I'll admit it, I hate having Visual Studios open if I don't need to. I'll also admit that I love me a user interface to create a database instead of writing scripts. Enter SQL Source Control. It integrates with Management Studios and whatever source control you have set up, so now you can commit changes from Management Studios after you make changes to the database, instead of having to go into VS and update the scripts and then deploy. A great tool for developers wanting to source control their databases.

**SQL Search**
  
A search engine for you database. Great for those days when you just can't remember the full name of the stored procedure you were working on.

Overall the SQL Toolbelt from Red Gate is more than worth what Red Gate's charging for it, and with every update they push they just get better and better. I highly recommend it for anyone doing development work in SQL Server, or DBA's needing to track development work.