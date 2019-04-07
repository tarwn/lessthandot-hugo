---
title: Only In A Database Can You Get 1000% + Improvement By Changing A Few Lines Of Code
author: SQLDenis
type: post
date: 2008-08-17T11:55:47+00:00
url: /index.php/datamgmt/datadesign/only-in-a-database-can-you-get-1000-impr/
views:
  - 39221
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - database
  - dates
  - indexing
  - performance tuning
  - rdbms
  - sql
  - t-sql
  - temporal data

---
Take a look at this query.

<pre>select * from

(

select customer_id, 'MTD' as record_type, count(*), sum(...), avg(...)

from payment_table


where year(payment_dt) = year(getDate())

and month(payment_dt) = month(getDate()) 

group by customer_id) MTD_payments

UNION ALL

(

select customer_id, 'YTD' as record_type, count(*), sum(...), avg(...)

from payment_table

where 

where year(payment_dt) = year(getDate())

group by customer_id) YTD_payments

UNION ALL

(

select customer_id, 'LTD' as record_type, count(*), sum(...), avg(...)

from payment_table) LTD_payments

) payments_report

order by customer_id, record_type</pre>

Can you see the problem?
  
A person had this query, it would run for over 24 hours. Wow, that is pretty bad, I don&#8217;t think I had ever written something that ran over an hour, and the ones I did were mostly defragmentation and update statistics jobs.

The problem is that the following piece of code

<pre>where year(payment_dt) = year(getDate())
and month(payment_dt) = month(getDate())</pre>

is not sargable. First what does it mean to be sargable? A query is said to be sargable if the DBMS engine can take advantage of an index to speed up the execution of the query (using index seeks, not covering indexes). The term is derived from a contraction of Search ARGument Able.

This query is not sargable because there is a function on the column, whenever you use a function on the column you will not get an index seek but an index scan. The difference between an index seek and an index scan can be explained like this: when searching for something in a book, you go to the index in the back find the page number and go to the page, that is an index seek. When looking for something in a book you go from page one until the last page, read all the words on all the ages and get what you need, that was an index scan. Do you see how much more expensive in terms of performance that was?

Let&#8217;s get back to the query, what can we do to make this piece of code use an index seek?

<pre>where year(payment_dt) = year(getDate())
and month(payment_dt) = month(getDate())</pre>

You would change it to this:

<pre>where payment_dt >= dateadd(mm, datediff(mm, 0, getdate())+0, 0)
and payment_dt < dateadd(mm, datediff(mm, 0, getdate())+1, 0)</pre>

You can see the complete question on the MSDN forum site here:
  
http://social.msdn.microsoft.com/Forums/en-US/transactsql/thread/d8464d33-38cc-48ba-bd15-5e7d14eeb19c

The Person said that his query went from over 24 hours to 36 seconds. Wow!! That is very significant. hardware cannot help you out if you have bad queries like that.

The same exact day I answered a very similar question, take a look here: http://social.msdn.microsoft.com/Forums/en-US/transactsql/thread/6107ec5c-b671-485f-941e-4efdf3d2fc66

The person had this

<pre>AND DATEDIFF(d, '08/10/2008', DateCreated) >= 0
AND DATEDIFF(d, DateCreated, '08/15/2008') >= 0</pre>

I told him to change it to this

<pre>AND DateCreated >= '08/10/2008'
and DateCreated < '08/16/2008'</pre>

And that solved that query. If you are interested in some more performance, I have written some [Query Optimization][1] items on the LessThanDot Wiki. Below are some direct links

<pre><a href="http://wiki.ltd.local/index.php/Case_Sensitive_Search" title="Case Sensitive Search">Case Sensitive Search</a>
<a href="http://wiki.ltd.local/index.php/No_Functions_on_Left_Side_of_Operator" title="No Functions on Left Side of Operator">No Functions on Left Side of Operator</a>
<a href="http://wiki.ltd.local/index.php/Query_Optimizations_With_Dates" title="Query Optimizations With Dates">Query Optimizations With Dates</a>
<a href="http://wiki.ltd.local/index.php/Optimization:_Set_Nocount_On" title="Optimization: Set Nocount On">Optimization: Set Nocount On</a>
<a href="http://wiki.ltd.local/index.php/No_Math_In_Where_Clause" title="No Math In Where Clause">No Math In Where Clause</a>
<a href="http://wiki.ltd.local/index.php/Don%27t_Use_%28select_%2A%29%2C_but_List_Columns" title="Don't Use (select *), but List Columns">Don't Use (select *), but List Columns</a></pre>

If you are interested in some blogposts about dates, take a look at these two which I wrote earlier
  
[How Are Dates Stored In SQL Server?][2]
  
[Do You Know How Between Works With Dates?][3]

 [1]: http://wiki.ltd.local/index.php/SQL_Server_Programming_Hacks_-_100%2B_List#Query_Optimization
 [2]: /index.php/DataMgmt/DataDesign/how-are-dates-stored-in-sql-server
 [3]: /index.php/DataMgmt/DataDesign/how-does-between-work-with-dates-in-sql-