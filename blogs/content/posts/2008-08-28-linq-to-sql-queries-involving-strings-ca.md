---
title: LINQ to SQL queries involving strings cause SQL Server procedure cache bloat
author: SQLDenis
type: post
date: 2008-08-28T14:12:25+00:00
url: /index.php/datamgmt/datadesign/linq-to-sql-queries-involving-strings-ca/
views:
  - 16523
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - bug
  - linq
  - performance

---
[Adam Machanic][1] created an item on connect explaining how LINQ to SQL queries involving strings cause SQL Server procedure cache bloat

_If an application is using LINQ to SQL and the queries involve the use of strings that can be highly variable in length, the SQL Server procedure cache will become bloated with one version of the query for every possible string length. For example, consider the following very simple queries created against the Person.AddressTypes table in the AdventureWorks2008 database:_

<pre>var p = 
                from n in x.AddressTypes 
                where n.Name == "Billing" 
                select n;

            var p = 
                from n in x.AddressTypes 
                where n.Name == "Main Office" 
                select n;</pre>

_If both of these queries are run, we will see two entries in the SQL Server procedure cache: One bound with an NVARCHAR(7), and the other with an NVARCHAR(11). Now imagine if there were hundreds or thousands of different input strings, all with different lengths. The procedure cache would become unnecessarily filled with all sorts of different plans for the exact same query. Even worse, imagine if a query used two or three different string parameters. The procedure cache could end up with hundreds of thousands or even millions of entries, the only differences being the variable lengths_

Wow that is bad indeed, please go to the connect site and vote for this so that Microsoft &#8216;fixes&#8217; this. Here is the URL: <https://connect.microsoft.com/VisualStudio/feedback/ViewFeedback.aspx?FeedbackID=363290>

 [1]: http://sqlblog.com/blogs/adam_machanic/default.aspx