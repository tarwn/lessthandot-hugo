---
title: "T-SQL Tuesday #20 – T-SQL Best Practices"
author: Jes Borland
type: post
date: 2011-07-12T10:37:00+00:00
ID: 1247
excerpt: "Here we go again! It's T-SQL Tuesday #20 – a monthly blog party, this time hosted by Amit Banerjee. (Thanks Amit!) This month, we're talking about 'T-SQL Best Practices'"
url: /index.php/datamgmt/dbprogramming/t-sql-tuesday-20-t/
views:
  - 8448
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
[![][1]][2]

Here we go again! It's T-SQL Tuesday #20 – a monthly blog party, this time hosted by Amit Banerjee. (Thanks Amit!) This month, we're talking about "T-SQL Best Practices". 

I used to write a lot more T-SQL than I do now. I had lots of rules for how T-SQL was written in my shop. But rather than sharing that extensive document, today I want to share with you my top three pieces of T-SQL advice. 

**KISS (Keep It Simple, Developer)** 

There are a lot of ways to solve problems in T-SQL. Once you get beyond a simple SELECT or UPDATE, you can really start playing. Derived tables, Common Table Expressions, CASE statements, aggregation, cursors, window functions, functions, pivoting...the things you can play with are endless. One of the beautiful things about T-SQL is that there is almost always more than one way to solve a problem. 

Remember, though, to keep it simple. Someday, someone else will need to read that code, and interpret it, and possibly (gasp) edit it. It could even be you. 

**Table Aliases** 

No, you don't need to ask people in the office to start calling you by your Twitter handle (no matter how funny that would be). SQL Server includes a "correlation name" or "range variable" to make FROM statements more easily readable. It's also known as a "table alias". 

A table alias is easy to implement, and saves trouble in the long run. How? Let's look at this simple query: 

```sql
SELECT Customer.CustomerID, SalesOrderID, OrderDate, TotalValue 
FROM Customer 
INNER JOIN SalesOrder ON SalesOrder.CustomerID = Customer.CustomerID
```

It's a pain to reference a field with the full table name. And while this query is readable, what table does "OrderDate" reside in? Can you easily tell? 

Try this version instead:

```sql
SELECT CUST.CustomerID, SO.SalesOrderID, SO.OrderDate, SO.TotalValue 
FROM Customer CUST 
INNER JOIN SalesOrder SO ON SO.CustomerID = CUST.CustomerID 
```

With a table alias: 

Right away, reading the SELECT statement, I can tell which tables the fields are being pulled from. 

When I want to use a field that is in multiple tables with the same name, it's faster to type the above then "Customer.CustomerID" or "SalesOrder.CustomerID". 

If I need to join a table to itself, I have to alias the table names. Easily understandable aliases are important in that situation. 

A few notes about table aliases: 

Make them easy to understand – "a", "b" and "c" are, usually, meaningless. 

Use aliases for every field – you don't know when you'll need to add another table to that query, and if that table may have a field of the same name. 

Be consistent – if you use "CUST" in one query, use it in the next. 

**Avoid Implicit Conversion** 

Every field in a table is assigned a data type – int, varchar, datetime, image, etc. When you join two tables together, or do comparisons in a WHERE clause, SQL Server is going to evaluate the data types of the two fields. If they are the same, no problem – they will happily and easily be matched or compared. 

But what happens when you try to compare an int and a varchar, or a varchar and a datetime? _(insert ominous music here)_ The dreaded implicit conversion. This will negatively impact the performance of your queries. I have (of course) been meaning to write a blog post on this for some time, but have not. One good blog on this is from Jonathan Kehayias ([twitter][3] | [blog][4]): [Unexpected Side Effects? Problems from Implicit Conversions][5]. Another informative blog post comes from Mike Walsh ([twitter][6] | [blog][7]): [You Could Be Suffering Right Now][8]. 

The easiest way to avoid implicit conversion is to have a well-designed database. However, if that is out of your control (perhaps it's a legacy database, or a vendor-supplied system), make sure you know what data types your fields are, and pay attention to that when writing code. 

**Don't Write Good Code, Write Great Code** 

By taking a little time to develop some standards and follow them, and understand the nuances of T-SQL, you can do more than write good code. You can write great code. 

I am excited to read the other T-SQL Tuesday entries!

 [1]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/olap_1.gif
 [2]: http://troubleshootingsql.com/2011/07/05/invitation-for-t-sql-tuesday-19-t-sql-best-practices/
 [3]: http://twitter.com/#!/SQLPoolBoy
 [4]: http://sqlskills.com/blogs/jonathan/
 [5]: http://sqlblog.com/blogs/jonathan_kehayias/archive/2009/07/16/unexpected-side-effects-problems-from-implicit-conversions.aspx
 [6]: http://twitter.com/#!/mike_walsh
 [7]: http://www.straightpathsql.com/
 [8]: http://www.straightpathsql.com/archives/2009/09/you-could-be-suffering-right-now/