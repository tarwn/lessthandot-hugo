---
title: SQL Server Developer Tips and Tricks
author: David Forck (thirster42)
type: post
date: 2012-04-23T12:25:00+00:00
ID: 1601
excerpt: |
  This blog is to share and highlight some of the tips and tricks that I've learned while using SQL Server the last few years.  Some of these are code oriented, database design,  or performance oriented, while others focus on personal development. 
  
  You&hellip;
url: /index.php/datamgmt/datadesign/sql-server-developer-tips-and/
views:
  - 12648
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Microsoft SQL Server

---
This blog is to share and highlight some of the tips and tricks that I've learned while using SQL Server the last few years. Some of these are code oriented, database design, or performance oriented, while others focus on personal development. Hopefully you'll learn at least one thig from this blog.

## You don't have to type out the columns

If you're using SQL Server Management Studios (SSMS) 2005 or higher, you can tell SSMS to script out select statements for you. To do this, right-click the table, go to Script Table As – Select To – New Query Editor Window . You can alternatively script to the clipboard if you already have a script open and just want to paste in there . This will open up a new window with your select statement. 

![scritping select to][1]

A bonus (or down side) is that SQL Server automatically wraps each column with brackets, so if your column names have odd characters (such as spaces) this will always work. Another bonus is consistency. Using this method you will always be sure to have all of the columns in the table, so if you're forgetful this method is perfect for you.

## Use a spreadsheet to help build your update statement

Sometimes you need to write a very long query that follows a certain pattern. One pattern could be an update statement comparing one table to another, and updating the destination table if there are any changes. The query could look something like this:

```sql
update dbo.DestinationTable
set
	Column1 = s.Column1,
	Column2 = s.Column2,
	Column3 = s.Column3,
	Column4 = s.Column4,
	Column5 = s.Column5,
	Column6 = s.Column6,
	Column7 = s.Column7,
	Column8 = s.Column8,
	Column9 = s.Column9,
	Column10 = s.Column10	
from dbo.DestinationTable d
	inner join dbo.SourceTable s
		on d.ID=s.ID	
where
	isnull(d.Column1,'null') <> isnull(s.Column1,'null')	or
	isnull(d.Column2,'null') <> isnull(s.Column2,'null')	or
	isnull(d.Column3,'null') <> isnull(s.Column3,'null')	or
	isnull(d.Column4,'null') <> isnull(s.Column4,'null')	or
	isnull(d.Column5,'null') <> isnull(s.Column5,'null')	or
	isnull(d.Column6,'null') <> isnull(s.Column6,'null')	or
	isnull(d.Column7,'null') <> isnull(s.Column7,'null')	or
	isnull(d.Column8,'null') <> isnull(s.Column8,'null')	or
	isnull(d.Column9,'null') <> isnull(s.Column9,'null')	or
	isnull(d.Column10,'null') <> isnull(s.Column10,'null')
```

Building an update statement like this can get pretty tiring, especially if you've got several that you need to write. A way to speed up this process is by copying the list of columns (which can be gotten using the steps outlined in "You don't have to type out the columns") into a premade spreadsheet that looks like this:

![premade excel spreadsheet][2]

You then copy the cells to the appropriate spots in your sql query and run a hand full of find and replace commands and voila, your update statement is done.

## Filters are your friends

Again, if you are using SSMS and working in a large database, SSMS has the functionality to filter what objects you can see. This makes working in a large database a lot easier because you can quickly find what you are looking for. To do this, right click on either the Tables, Views, or Stored Procedures Folder, select Filter – Filter Settings

![filters!][3]

In the screen that pops up, you can set the filter in many different ways.

![filter settings][4]

Play with this to find out what works best for you. At my organization we have what are called Code Generated Stored Procedures. All of these stored procedure's names start with "_". So to only look at custom code I tell the filter to only show stored procedures that don't contain an underscore.

## Make sure your relationships are set up

One of the easiest things to do when setting up a new database is to forget to set up the relationships between tables. In a large database or during development of an application, it's easy to forget to set up the relationship for a new table. LessThanDot's SQLCop has a section for detecting missing foreign keys (as well as other nifty things). Get it for free here: http://sqlcop.lessthandot.com/

## Tools are your best friends

While SQL Server Management Studios is far and above a better tool than trying to do everything in a command prompt, it doesn't do everything for you when it comes to managing deployments. There are several tools out there that can do this for you. Red Gate's SQL Developer Bundle is a collection of their database tools. It's a bit pricy, but the ROI is phenomenal and is worth having for every database developer. They even offer a free trial. Check it out here: [SQL Developer Bundle][5]

Another nifty tool is SQL Sentry's Plan Explorer. Plan Explorer gives you a detailed layout of what's going on with your sql query. It'll tell you which parts are costing the most performance wise. There's a free version and a version you have to purchase. Check them out here: [SQL Server Query View][6]

### Get a Code Generator

Gode generators are another tool that can vastly reduce the amount of time it takes to develop a database. Where I work at one of our application developers took the time to create an in-house code generator for our databases. It iterates through a database and creates insert, update, delete, and select statements for each table. This internal tool has saved us a lot of time and energy by providing a consistent base to start from. I don't know a lot about what code generators are available, but I highly suggest trying to find one that works for you.

## Don't be afraid to ask

One of the biggest mistakes a developer can do is not ask a question, no matter how small and trivial it may seem. There are numerous resources online for getting answer. Here's my personal list of where I go for answers:

  * www.lessthandot.com
  * www.stackoverflow.com
  * www.dba.stackexchange.com
  * www.sqlservercentral.com
  * [#sqlhelp on twitter][7]

## Follow someone

Finding someone that you greatly enjoy listening to and reading content from can greatly improve your overall abilities and knowledge pool. About half a year after I started database development I stumbled upon SQLDenis' blog on LessThanDot. I started following him on there and I've learned quite a bit from him. About a year or so ago I started following Brent Ozar, and I've learned more about the hardware side of SQL Server and other DBA features than I would have otherwise. Now that Kendra Little and Jeremiah Peschka are blogging for Brent Ozar PLF, the blogs on www.brentozar.com have become even more diverse.

The SQL Server community is blessed with many active community members that are more than willing to share their knowledge with the rest of the world. Follow one of them and you'll be thanking yourself later.

Ted Krueger wrote a blog about mentors and mentoring here: [Mentoring As I See It][8]

## Schemas, it's what's for dinner

In SQL Server 2005+, you can break out tables, views, and stored procedures into schemas. A schema is a security object that allows you to separate objects, similar to folders on your hard drive. You can't nest schemas though, but they are still pretty nifty. This leans a bit more towards administration, but I believe that database developers should know how to utilize schemas.

Let's say you've got a database with 10 tables, and out of those 10 tables your users can insert, update, and delete against 5 of them. The other 5 they can only read from. In a database where they're all in the dbo schema, it'd look something like this:

![tables without schemas][9]

To set up proper security you'd have to assign security by table (read permissions and read/write permissions). This is somewhat messy and doesn't update if new tables are added. A better way is to break the tables into two schemas, a read only schema and a read/write schema. The following is an example of how it could look:

![tables with schemas][10]

With this set up, you can now assign security at the schema level, and every new table added now has security set up on it. Easy, right? Play with schemas and see how you can utilize them to your advantage.

## Fine Tune SSMS Options

SQL Server Management Studios has a lot of options to play with. One option that I have disabled is the "Use [database]" statement that you get whenever you script out a table. To change this I went to Tools – Options. Then went to SQL Server Object Explorer – Scripting, and changed "Script USE [database]" to false.

![getting to the options][11]

![options window][12]

There are loads of options that you can choose from. Some examples include:

  * Hiding system objects in Object Explorer
  * Enabling/disabling Line Numbers
  * Advanced execution settings (set nocount, set noexec, etc.)
  * Results to Grid, Text, or File

That's all that I can think of for now. If you've got any tips or tricks, or a favorite person that you follow, please share it in the comments below, or even write your own blog and link us to it.

 [1]: /wp-content/uploads/blogs/DataMgmt/thirster42/DevTT/devtt1.png "scripting select to"
 [2]: /wp-content/uploads/blogs/DataMgmt/thirster42/DevTT/devtt2.png "premade excel spreadsheet"
 [3]: /wp-content/uploads/blogs/DataMgmt/thirster42/DevTT/devtt3.png "filters!"
 [4]: /wp-content/uploads/blogs/DataMgmt/thirster42/DevTT/devtt4.png "filter settings"
 [5]: http://www.red-gate.com/products/sql-development/sql-developer-bundle/
 [6]: http://www.sqlsentry.net/plan-explorer/sql-server-query-view.asp
 [7]: https://twitter.com/#!/search/%23sqlhelp
 [8]: /index.php/ITProfessionals/ProfessionalDevelopment/mentoring-as-i-see-it
 [9]: /wp-content/uploads/blogs/DataMgmt/thirster42/DevTT/devtt5.png "tables without schemas"
 [10]: /wp-content/uploads/blogs/DataMgmt/thirster42/DevTT/devtt6.png "tables with schemas"
 [11]: /wp-content/uploads/blogs/DataMgmt/thirster42/DevTT/devtt7.png "getting to the options"
 [12]: /wp-content/uploads/blogs/DataMgmt/thirster42/DevTT/devtt8.png "options window"