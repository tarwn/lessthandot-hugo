---
title: Free SQL Server Tools
author: Jes Borland
type: post
date: 2012-08-31T14:05:00+00:00
ID: 1706
excerpt: |
  I recently bought a new laptop – a Lenovo X230. It has an Intel Core i5-3360M, 16 GB RAM, and an OCZ Vertex 3 SSD. It's powerful and wonderful. And fast. Very fast.
  In the process of installing my programs, I started thinking about all the software I u&hellip;
url: /index.php/datamgmt/dbprogramming/free-sql-server-tools/
views:
  - 45889
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
  - SSRS

---
I recently bought a new laptop – a Lenovo X230. It has an Intel Core i5-3360M, 16 GB RAM, and an OCZ Vertex 3 SSD. It's powerful and wonderful. And fast. Very fast.

In the process of installing my programs, I started thinking about all the software I use to make my job easier. There are a lot of great programs available for DBAs and developers. The best ones, of course, are free. Here's a list of my five favorite free SQL Server tools!

[SSMS Tools Pack][1]

This SQL Server Management Studio plug-in is developed by Mladen Prajdic. It has several very useful features to help DBAs and developers, such as:

  * SQL Query History – You open a tab in SSMS, write a query, get your results, close it, and choose not to save changes. Five seconds or five weeks later, you realize you need that query again. Why rewrite it? Simply search for your query in the history. This has saved me hours of work over the years. 
  * New Query Template – I want all my queries to have my name, the date, and what the purpose of the query is. I also want to make sure BEGIN TRAN and ROLLBACK are there, along with COMMIT (commented out). It was a pain to type that every time. But using the New Query Template, it's there automatically, every time I open a new window. 
  * Window Connection Coloring – Have you ever thought, "It would be really nice to have a red bar across the top of windows opened against production instances, so I don't accidentally run a query against them."? Your wish has come true. With Connection coloring, you can set up color-coding for individual or groups of servers. This has saved my bacon more than once. 
  * Generate Insert Statements – SSMS will generate create, drop, and insert statements, but none of these options will script out the data so it can be recreated in another environment. With this tool installed, simply right-click a table and generate an insert statement for the values. Yes, it's that simple. 

Those are only a few of the awesome options included. Make sure to check out the tool and all the cool benefits it can offer you. (And donate to Mladen. He does this for free, on his own time, and it's worth the money.)

[SQL Sentry Plan Explorer][2]

Hopefully you are taking full advantage of SQL Server's query execution plans for diagnosing issues and performance tuning. It's a great start, but those plans can be hard to read. How do you know which operators are the highest cost? How can you search them? What about those ugly plans that have ten layers?

SQL Sentry has given us Plan Explorer for free. This takes execution plans from this:

 

<p style="text-align: center;">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/query plan ssms.JPG?mtime=1346429007" alt="" />
</p>

To this:

 

<p style="text-align: center;">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/query plan plan explorer.JPG?mtime=1346429007" alt="" />
</p>

Even on a very simple query, how much easier is that to read? It's so simple to identify which operations have the highest cost. Download this today, and make sure to thank SQL Sentry for it!

[Red Gate SQL Search][3]

You know that somewhere in your database, you have a stored procedure that references that one view that links back to that table you're working on. The problem is finding it. Or perhaps you need to change the data type of a field, and you need to change it across multiple tables. Do you know all of the tables it's used in?

Red Gate's awesome SQL Search tool will let you search across tables, views, stored procedures, and functions to find text. You can easily view the text of a stored procedure or function.

 

<p style="text-align: center;">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/sql prompt.JPG?mtime=1346429007" alt="" />
</p>

Download this today!

[format-sql.com][4]

Admit it: not all of your T-SQL code is pretty. This website is a lab project from Red Gate. It takes your T-SQL and formats it into very readable code. It highlights syntax errors.

Bonus: it works on tablets and smartphones.

[Reporting Services Scripter][5]

I don't work with SSRS as much as I used to, but I still rely on this program. It's designed to take the objects from an SSRS folder, script them out, and run the scripts on another folder. This is helpful for migrations, moves, and disaster recovery. You can script subscriptions, which is really helpful. It's a great way to back up reports before making changes. In other words, if you're responsible for even on SSRS installation, I'd suggest downloading this and using it!

 

Have any other free tools you like to use? Leave me a note in the comments!

 [1]: http://www.ssmstoolspack.com/
 [2]: http://www.sqlsentry.com/plan-explorer/sql-server-query-view.asp
 [3]: http://www.red-gate.com/products/sql-development/sql-search/
 [4]: http://www.red-gate.com/labs/format-sql/
 [5]: http://www.sqldbatips.com/showarticle.asp?ID=62