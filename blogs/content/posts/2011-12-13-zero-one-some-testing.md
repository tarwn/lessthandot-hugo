---
title: Zero-One-Some Testing
author: George Mastros (gmmastros)
type: post
date: 2011-12-13T11:33:00+00:00
ID: 1441
excerpt: |
  I met Denis and Sebastian over a year ago when I attended their session on test driven database development.  Since then, I have been using tSQLt to add unit tests to my database.  The following post was written by Denis Lloyd Jr.
  
  
  About the Series&hellip;
url: /index.php/datamgmt/datadesign/zero-one-some-testing/
views:
  - 18560
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
tags:
  - tsqlt
  - unit testing

---
_I met Denis and Sebastian over a year ago when I attended their session on test driven database development. Since then, I have been using tSQLt to add unit tests to my database. The following post was written by Denis Lloyd Jr._

## About the Series

Test case heuristics are patterns used to help decide the next test to write and ensure test coverage of requirements. This is the second post on a series of test case heuristics pertaining to database testing.

I'm trying out a new way of delivering series to a wider audience – post sharing! The series home is at: http://testdrivendatabases.com/test-heuristics where you can find links to all articles in the series. The posts will be scattered over a variety of websites and blogs.

## Definition:

Zero-One-Some says that if multiple instances of a value are allowed, then there should be a test for zero of them; one of them; and some of them. Zero-One-Some is sometimes referred to as Zero-One-Many and is often related to cardinality in the database.

For example, a view may return multiple records. When testing the view, a test should be written where we expect zero records returned; another test for exactly one record returned; and another test for several rows returned.

<div class="image_block">
  <a href="/media/blogs/DataMgmt/ZeroOneZomeTesting.png?mtime=1323782210"><img src="/wp-content/uploads/blogs/DataMgmt/ZeroOneZomeTesting.png?mtime=1323782210" alt="" width="898" height="240" /></a>
</div>

Zero-One-Some may be considered both on the input (e.g. a loop that may process multiple values) or the output (e.g. a query that returns multiple rows).

## Purpose:

Zero-One-Some testing helps:

  * Focus on correct behavior when there are multiple inputs or outputs
  * Clarify the requirements when zero records should be processed; a common source of database defects.
  * Prevent mistakes when using grouping in queries

## Example:

The business would like a report of the number of orders and the total revenue from those orders for each of the last 3 months. The report may look like this:

<pre>Month             Number of Orders              Revenue from Orders
Nov 2011          52                            $3582.00
Oct 2011          70                            $12399.50
Sep 2011          30                            $899.55</pre>

It is clear from this requirement that multiple orders must be processed. By applying zero-one-some, we are forced to ask the following questions:

  * If there are no orders in the past 3 months, what should the report display? Should it list each month with 0 orders and $0?
  * If there are no orders for any particular month, should that month still be listed in the report?
  * Aggregations are always interesting spots to test. If there is a null value for an order amount, how should that be treated in the sum? If it's not included in the revenue, should it also not be included in the count?

## Notes:

Tests for zero records seem to uncover missing requirements or defects in code involving aggregations or in places where programmers assume that there will simply be data (perhaps because their test database already has data in it).

Whereas tests for one and some records seem to uncover more problems in loops when a specific exit condition is needed.

Tests for multiple (“some”) records may also be useful when data can be duplicated. Often we assume that data being processed is unique, but asking the question, “what if there are multiple instances of the same record?” can be illuminating.

## Special Cases:

**Joins**: When multiple tables are joined together in a query, we must often consider the cardinality of the relationship between the tables. Is there a one-to-one relationship between the tables (and is that relationship enforced)? How about a one-to-many or a many-to-many relationship? These impact what tests are needed.

The join type (e.g. inner, left or right outer, full) must also be considered. These are a few of the possibilities:

  * A record exists in the left table, but there are no matches in the right table.
  * A record exists in the left table and there is exactly one match in the right table.
  * A record exists in the left table and has multiple matches in the right table.
  * A record exists in the right table, but has no matches in the left table.
  * And so on...

**Filters**: Zero-one-some is also particularly useful in filters, such as WHERE clauses. Consider the following sub-query, for example:

```sql
SELECT Name   
FROM OrderMgmt.Customer  
WHERE CustomerId =        
    (SELECT CustomerId          
       FROM OrderMgmt.Order         
      WHERE OrderId = @OrderId)  
```
While this is a simplistic case, the programmer is likely expecting exactly one record to be returned from the sub-query. If the sub-query returns zero or multiple records though, the actual behavior of this query may not be so pleasant.