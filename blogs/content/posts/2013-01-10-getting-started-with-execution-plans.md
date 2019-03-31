---
title: Getting started with Execution Plans
author: Axel Achten (axel8s)
type: post
date: 2013-01-10T07:52:00+00:00
excerpt: |
  Another document for the customer's DBA checklist. This time we are going to look how we find and read an Execution Plan from a query.
  Showing an Execution Plan
  I will only focus on the graphical Execution Plans and in the examples the AdventureWorks2&hellip;
url: /index.php/datamgmt/dbprogramming/getting-started-with-execution-plans/
views:
  - 11497
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - execution plan
  - query plan

---
Another document for the customer&#8217;s DBA checklist. This time we are going to look how we find and read an Execution Plan from a query.

**Showing an Execution Plan**
  
I will only focus on the graphical Execution Plans and in the examples the AdventureWorks2008R2 OLTP database is used which you can download here.
  
To see a Query Execution plan, you need to create a query:

<pre>SELECT * FROM Sales.vStoreWithContacts</pre>

And then you click the &#8220;Display Estimated Execution Plan&#8221; or the &#8220;Include Actual Execution Plan&#8221; button in the SQL Editor Toolbar of SQL Server Management Studio.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/EP1.JPG?mtime=1357810925"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/EP1.JPG?mtime=1357810925" width="730" height="129" /></a>
</div>

The difference between the Estimated and the Actual plan is the execution of the query itself. When you hit the &#8220;Display Estimated Execution Plan&#8221; button, SQL Server Management Studio will immediately show you an Execution plan, however it&#8217;s an estimated plan and the query itself is NOT executed. When you hit the &#8220;Include Actual Execution Plan&#8221; nothing will happen until you execute the query. After execution of the query a third tab will appear in SSMS showing the Execution Plan that was actually used:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/EP2.JPG?mtime=1357810925"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/EP2.JPG?mtime=1357810925" width="1651" height="711" /></a>
</div>

**Reading Execution Plans**
  
When you want to read the above Execution Plan you have to read from right to left and from bottom to top. So SQL Server will start with

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/EP3.JPG?mtime=1357810926"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/EP3.JPG?mtime=1357810926" width="232" height="200" /></a>
</div>

And end with

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/EP4.JPG?mtime=1357810926"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/EP4.JPG?mtime=1357810926" width="91" height="86" /></a>
</div>

Other information you directly can see is the most expensive operation and based on the thickness of the connecting arrows you can see the amount of data thatï¿½s streaming through them:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/EP5.JPG?mtime=1357810926"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/EP5.JPG?mtime=1357810926" width="783" height="226" /></a>
</div>

**Finding more information**
  
When you hover your mouse over the arrows or operations in an Execution Plan a pop-up will appear giving you more detailed information about the rows or operation:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/EP6.JPG?mtime=1357810926"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/EP6.JPG?mtime=1357810926" width="225" height="270" /></a>
</div>

Needless to say that the Actual Number of Rows and some other information will not be available in an Estimated Execution Plan. In the example above we see a big difference between the Estimated and the Actual Number of Rows which might indicate that our statistics are out of date.
  
For even more information select an operation and open the Properties Window (F4):

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/EP7.JPG?mtime=1357810926"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/EP7.JPG?mtime=1357810926" width="400" height="225" /></a>
</div>

Another option to get more out of your Execution Plans is to download [SQL Sentry Plan Explorer][1] more information is found at their site but here is a screenshot from the same Execution Plan in the Free Plan Explorer:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/EP8.JPG?mtime=1357810926"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/EP8.JPG?mtime=1357810926" width="1267" height="502" /></a>
</div>

As you can see, the most expensive operation is highlighted in red and the tool also highlights the difference between the estimated and actual rows.

**Missing index information**
  
When executing the following query:

<pre>SELECT * FROM Sales.vStoreWithContacts
WHERE Firstname = 'Alan'</pre>

You’ll see some green text in the top of the Execution Plan:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/EP9.JPG?mtime=1357810926"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/EP9.JPG?mtime=1357810926" width="1081" height="77" /></a>
</div>

This information comes from the Dynamic Management View: sys.dm\_db\_missing\_index\_details. More information on Indexes is out of scope for this post but be careful with just implanting these Missing Indexes. They might improve the performance of this query but since they need to be maintained they can also slow down other operations on the database.

**Comparing Execution Plans**
  
Are you rewriting a query and wondering which one should perform better? You can easily compare the Execution Plans of two queries. Just write these queries in one query window:

<pre>SELECT * FROM Sales.vStoreWithContacts
WHERE Firstname NOT LIKE 'A%';

select * FROM Sales.vStoreWithContacts
WHERE FirstName IN (SELECT FirstName FROM Sales.vStoreWithContacts
WHERE FirstName NOT LIKE 'A%');
GO</pre>

When you execute the queries you’ll see they both return the same 70 rows but when you look at the header of the Execution Plans you’ll see that the first query costs less than the second one in this batch:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/ep10.JPG?mtime=1357810927"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/ep10.JPG?mtime=1357810927" width="275" height="150" /></a>
</div>

**Conclusion**
  
Execution Plans gives us valuable information and a deep insight in how SQL Server executes the queries we write. Reading and understanding Execution Plans are essential for writing well performing queries.

 [1]: http://www.sqlsentry.com/plan-explorer/sql-server-query-view.asp