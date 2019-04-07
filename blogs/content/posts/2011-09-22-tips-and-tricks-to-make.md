---
title: Tips and Tricks to Make SQL Server Management Studio Awesome
author: Jes Borland
type: post
date: 2011-09-22T10:51:00+00:00
ID: 1324
excerpt: SQL Server Management Studio is the de facto tool for working with SQL Server. But its default options may not always be the best way for you to work. After working with it for years, I’ve come up with a list of my favorite features.
url: /index.php/datamgmt/dbprogramming/tips-and-tricks-to-make/
views:
  - 124601
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
SQL Server Management Studio is the de facto tool for working with SQL Server. But its default options may not always be the best way for you to work. After working with it for years, I’ve come up with a list of my favorite features. 

(Secret: This is also my way of recording this so that the next time I need to set up a new PC, I just have to come back here.) 

**Part I: The Options Menu** 

In SSMS, go to Tools > Options. 

**> Environment**
  
**>> Keyboard:** Here you can set up “Query shortcuts”. By default, sp\_help, sp\_who and sp_lock are there. I’ve added things like [sp_whoisactive][1]. Are there other queries you use regularly? Set up shortcuts here, to save yourself the trouble of having to open a script file, or (gasp) typing them each time.

 ![][2]

**> Text Editor**
  
**>> All Languages**
  
**>>> General**
  
**>>>> Word wrap:** I’m not sure where I would be without this setting. Who wants to scroll back and forth constantly? 

![][3]

**>>>> Line numbers:** it’s just what it means. Go from this:

 ![][4]

to this: 

![][5]

**>>> Transact-SQL**
  
**>>>> IntelliSense:** turn on IntelliSense to have SSMS help you complete field names, function names, and more. Users of Visual Studio will be used to this functionality. Note: this will only work in SSMS 2008 and above, against SQL Server 2008 and greater databases. 

![][6]

**>>> Editor Tab and Status Bar**
  
This can be broken into two sections. 

**>>>> Status Bar Content and Status Bar Layout and Colors:** control how that bar that is at the bottom of query execution window (by default) appears. 

![][7]

![][8]

**>>>> Tab Text:** controls what information appears in the tab at the top of a query. 

 ![][9]

![][10]

**> Query Results**
  
**>> SQL Server:** Here, I can choose the default location for saving my results, if I don’t want to save it in the location Microsoft specifies. 

![][11]

**>>> Results to Grid:** Here, I like to choose a few non-standard options. 

 ![][12]

“Include column headers when copying or saving the results” means just that. When I right-click a result-set, copy it, then paste it, the column headers will go with it. 

“Display results in a separate tab” – when I execute my query, instead of the results being in the lower half, they are on a second tab. I can even choose to switch to that automatically. This is especially useful for presentations. 

Instead of this: 

![][13]

I see this: 

![][14]

**> SQL Server Object Explorer**
  
**>> Commands:** In Object Explorer, you can right-click a table or view and choose to select or edit the top X rows. Here, you can set the values for these options. 

![][15]

**>> Scripting:** there is a wealth of options here that will turn things on or off when you right-click an object and choose to Script To…

Some of my favorites: Include descriptive headers, Script USE database, Include IF NOT EXISTS clause, and Schema qualify object names. Take the time to look through this list and understand what you can turn on and off in SSMS. 

![][16]

**> Designers**
  
**>> Table and database designers:** I uncheck the “Prevent saving changes that require table re-creation” option. 

![][17]

**Part II: Object Explorer** 

There are a couple cool things you can do with Object Explorer that I find people are unaware of. 

First, when you are writing a query, especially against an older database that doesn’t have IntelliSense support, you can drag table, view, column and stored procedure names to the query pane instead of typing them. 

![][18]

Dragging the “Columns” across to the query pane will produce this: 

 ![][19]

Second, you can filter the items you see in a database. The little blue funnel at the top of Object Explorer will help you do this. 

![][20]

Here, I’m going to filter on the “Person” schema. 

![][21]

My results are much easier to read. This isn’t a huge deal in AdventureWorks, but when you get into databases with hundreds or thousands of objects, this is very handy. 

![][22]

And third, when you run a script and have an error, most times you can double-click it to be taken to the line that generated the error. (That one? Hard to demonstrate with a screenshot. Next plan: video blogging!) But, give it a try. 

**Part III: Must-Have Third-Party Add-Ins**

SSMS Tools Pack &#8211; <http://www.ssmstoolspack.com/>

This free add-in extends the functionality of SSMS. A lot. Some of its features that I love:

Window Connection Coloring – want to have a green bar at the top of queries against development servers, yellow on QA, and red on production? Just set it up. 

New Query Template – want to have the same text appear in every new query you open up? Me too. Create a template. 

Want to have your query history saved to a text file or a database table? It’s all here. 

And that’s just scratching the surface. Download this tool! 

![][23]

Extended Events Manager &#8211; <http://extendedeventmanager.codeplex.com/> 

Are you using Extended Events yet? You should be. Sure, the down side is that there is no GUI for it (yet). However, this tool starts to bridge the gap. You can view event sessions, start and stop them, drop them, and script out operations. 

![][24]

**Making the Most of Your Tools** 

The defaults provided by Microsoft are not always the most efficient, or the most helpful to you. Explore the settings in SSMS and make _it_ work for _you_!

 [1]: http://sqlblog.com/blogs/adam_machanic/archive/tags/sp_5F00_whoisactive/default.aspx
 [2]: /wp-content/uploads/users/grrlgeek/SSMSTips1.JPG?mtime=1316658886 ""
 [3]: /wp-content/uploads/users/grrlgeek/SSMSTips2.JPG?mtime=1316659457 "null"
 [4]: /wp-content/uploads/users/grrlgeek/SSMSTips3.JPG?mtime=1316659555
 [5]: /wp-content/uploads/users/grrlgeek/SSMSTips4.JPG?mtime=1316659700
 [6]: /wp-content/uploads/users/grrlgeek/SSMSTips5.JPG?mtime=1316659979
 [7]: /wp-content/uploads/users/grrlgeek/SSMSTips6.JPG?mtime=1316660151
 [8]: /wp-content/uploads/users/grrlgeek/SSMSTips7.JPG?mtime=1316660272
 [9]: /wp-content/uploads/users/grrlgeek/SSMSTips8.JPG?mtime=1316660416
 [10]: /wp-content/uploads/users/grrlgeek/SSMSTips9.JPG?mtime=1316660495
 [11]: /wp-content/uploads/users/grrlgeek/SSMSTips10.JPG?mtime=1316660627
 [12]: /wp-content/uploads/users/grrlgeek/SSMSTips11.JPG?mtime=1316660758
 [13]: /wp-content/uploads/users/grrlgeek/SSMSTips12.JPG?mtime=1316660958
 [14]: /wp-content/uploads/users/grrlgeek/SSMSTips13.JPG?mtime=1316661051
 [15]: /wp-content/uploads/users/grrlgeek/SSMSTips14.JPG?mtime=1316661148
 [16]: /wp-content/uploads/users/grrlgeek/SSMSTips15.JPG?mtime=1316661241
 [17]: /wp-content/uploads/users/grrlgeek/SSMSTips16.JPG?mtime=1316661418
 [18]: /wp-content/uploads/users/grrlgeek/SSMSTips17.JPG?mtime=1316661526
 [19]: /wp-content/uploads/users/grrlgeek/SSMSTips18.JPG?mtime=1316661662
 [20]: /wp-content/uploads/users/grrlgeek/SSMSTips19.JPG?mtime=1316661747
 [21]: /wp-content/uploads/users/grrlgeek/SSMSTips20.JPG?mtime=1316661863
 [22]: /wp-content/uploads/users/grrlgeek/SSMSTips21.JPG?mtime=1316662001
 [23]: /wp-content/uploads/users/grrlgeek/SSMSTips22.JPG?mtime=1316662174
 [24]: /wp-content/uploads/users/grrlgeek/SSMSTips23.JPG?mtime=1316662298