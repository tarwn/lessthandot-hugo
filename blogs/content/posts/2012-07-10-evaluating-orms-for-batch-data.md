---
title: Evaluating ORMs for Batch Data Performance
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2012-07-10T10:32:00+00:00
excerpt: "Earlier this week I came upon a post (Entity Framework Comparative Performance) by Luke McGregor that compared the performance of several ORMs for handling batch data. Given the amount of batch data I've processed, I was curious how those ORM tests would line up against a couple common non-ORM methods."
url: /index.php/enterprisedev/orm/evaluating-orms-for-batch-data/
views:
  - 16017
rp4wp_auto_linked:
  - 1
categories:
  - ORM
tags:
  - orm
  - sql server

---
Earlier this week I came upon a post ([Entity Framework Comparative Performance][1]) by Luke McGregor ([b][2]|[t][3]) that compared the performance of several ORMs for handling batch data. Given the amount of batch data I&#8217;ve processed, I was curious how those ORM tests would line up against a couple common non-ORM methods.

I decided to stick to ADO.Net methods for data and to focus on the insert, as a fast insert can be used to replace updates and deletes. SSIS and bcp would be alternative options, but would require additional setup to test alongside the .Net code.

<div style="background-color: #dddddd; font-style: italic; padding: .5em; margin: bottom: .5em">
  2012-07-11: In response to a comment below, I added a followup section to the post with a graph showing all the test results in the 100,000 record tests.
</div>

## Method

The method for these tests closely resembles the one Luke followed in his original post. The main exception is that I am using a local SQL Server 2008 R2 instance rather than a remote VM. Unfortunately attempts to run a full test set against my remote SQL Server VM ran into errors each time I tried, generally in the Entity Framework setup, teardown, and assertion code. My local system has a large enough amount of RAM and cores that any impact from running the tests locally should be limited, with only the network constraint removed from the equation.

## Tests

I focused entirely on the insert tests, adding two new tests to use SqlDataAdapter and SqlBulkCopy. The test lineup then became:

  * EntityFramework 4.1 
      * Basic Configuration (No optimizations)
      * AutoDetectChanges Disabled
      * Tuned
  * EntityFramework 5.0 beta1 
      * Basic Configuration (No optimizations)
      * AutoDetectChanges Disabled
      * Proxy Entities
      * Tuned
  * Dapper 1.8 
      * Dapper Rainbow
      * My Best effort at making it go fast
      * EDIT Batching inserts/updates using transactions
  * LINQ to SQL 
      * Basic Configuration (No optimizations)
      * Tuned
  * Raw ADO methods 
      * Basic SQL Command
      * SQL Command w/ Transaction
      * [SqlDataAdapter][4]
      * [SqlBulkCopy][5]

_The raw code is available on my branch at github: <https://github.com/tarwn/StaticVoid.OrmPerformance> and the original is located on Luke&#8217;s here: <https://github.com/lukemcgregor/StaticVoid.OrmPerformance>._

### SqlDataAdapter Method

The SqlDataAdapter method is to create a local DataSet or DataTable then provide this to a SqlDataAdapter with a configured SqlCommand object and parameters. The SqlDataAdapter takes care of the details, getting all the individual rows of data into the database via that insert command. Because we are operating on the full set of data, the test stores all of the values in memory until it is told to commit them.

### SqlBulkCopy Method

The SqlBulkCopy object is designed to &#8220;let you efficiently bulk load a SQL Server table&#8221; ([MSDN][6]). It provides similar functionality as bcp, but from inside our .Net code and without the format files. This test works similarly to the SqlDataAdapter test, in that it holds the entire DataTable of test data in memory until it is told to commit it in one go (some of the tests are iterative, some are bulk, the test method caters to both).

## Results

I&#8217;ll admit the results were not that surprising. SqlBulkCopy was the fastest method for inserting larger amounts of data, but had some initial overhead that made it slower for the 1, 10, and 100 record tests. Compared to the best times from the other methods (SqlBulkCopy is the SqlCommand representative in the chart), the performance difference is clear:

<div style="color: #666666; text-align: center; font-size: 90%">
  <img src="http://tiernok.com/LTDBlog/ORM/Graph-1.png" alt="Graph of Best Times for 1-10000 records" /><br /> Best Times for 1, 10, 100, 1000, 10000 scenarios
</div>

Extending this to a larger set of 100000 records and the difference is relatively the same. Relative to the prior set of results, SqlBulkCopy is not as much faster on the 100,000 run as it was on the 10,000. It would be interesting to switch to increments of 10,000 and see if there is a pattern to it.

<div style="color: #666666; text-align: center; font-size: 90%">
  <img src="http://tiernok.com/LTDBlog/ORM/Graph-2.png" alt="Graph of Best Times for 1-100000 records" /><br /> Best Times for 1, 10, 100, 1000, 10000, 100000 scenarios
</div>

I also thought it was interesting to see how well the tuning improved some of the ORM methods. In the case of Entity Framework, it&#8217;s clear that if you intend to use it for batch data then tuning is a requirement, not an option. The out-of-the-box experience for Entity Framework 4.1 and 5 were roughly an order of magnitude slower than all other tests.

<div style="color: #666666; text-align: center; font-size: 90%">
  <img src="http://tiernok.com/LTDBlog/ORM/Graph-3.png" alt="Scaled out to show EF 4.1 and 5 Basic Performance" /><br /> Scaled out to show EF 4.1 and 5 Basic Performance
</div>

The other key indicator is memory. Our two new methods store all the data and send it in a single command, so they will have a higher memory footprint to accommodate that data. The methods that incrementally send the data, like the Dapper scenarios and basic SqlCommand option, will use very little data since they are flushing each addition directly to SQL.

<div style="color: #666666; text-align: center; font-size: 90%">
  <img src="http://tiernok.com/LTDBlog/ORM/Graph-4.png" alt="Memory Usage per Test Type" /><br /> Memory Usage per Test Type
</div>

This graph shows the memory/record of the 1000, 10000, and 100000 test runs. As we would expect, the memory/record for the full batch methods is reduced as the overhead is spread across more records. Entity Framework show consistently high memory usage, but the Proxy Entities method does bring it down to just about twice as much as Linq2SQL, which is in turn about 50% higher than the SqlCommand/SqlBulkCopy methods.

## Conclusions

For pure, batch insertions, I still wouldn&#8217;t use an ORM. These tests show that the ORMs tested are still significantly slower at batch insertion than tools built specifically for bulk operations, like SqlBulkCopy. We&#8217;ve also seen that when we do use an ORM, understanding it&#8217;s performance characteristics and how to tune the ORM can make an enormous difference in how well or poorly it works.

## Follow-up

Based on Tudor&#8217;s comment below, I&#8217;ve generated a graph of the times for each method in the 100,000 record tests. Unlike the full chart above, I&#8217;ve scaled it to ignore the two basic Entity Framework entries that cause the line chart above to be so unreadable. 

<div style="color: #666666; text-align: center; font-size: 90%">
  <img src="http://tiernok.com/LTDBlog/ORM/Graph-Followup.png" alt="Readable Execution Times for 1000,000 records" /><br /> Readable Execution Times for 1000,000 records
</div>

Using ADO.Net does not automatically mean better performance than an ORM. SqlBulkCopy does clearly perform better, but using a SqlCommand.ExecuteNonQuery or a SqlAdapter.InsertCommand does not achieve the same level of performance. Many of the ORM tests kept up or outperformed the non-transactional SqlCommand and SqlAdapter tests, and Dapper kept up with the Transactional SqlCommand test. ADO.Net itself is not giving the boost in speed we see from SqlBulkCopy, it&#8217;s the use of a tool that is built specifically for batch processing (all of the rest operate at the row level, ADO.Net or ORM).

 [1]: http://blog.staticvoid.co.nz/2012/03/entity-framework-comparative.html "Entity Framework Comparative Performance at static void; blog"
 [2]: http://blog.staticvoid.co.nz/ "static void; blog"
 [3]: https://twitter.com/staticv0id "staticv0id on twitter"
 [4]: https://github.com/tarwn/StaticVoid.OrmPerformance/blob/master/Harness.SqlCommand/InsertViaDataAdapterConfiguration.cs "Code for the SqlDataAdapter Scenario"
 [5]: https://github.com/tarwn/StaticVoid.OrmPerformance/blob/master/Harness.SqlCommand/InsertSqlBulkConfiguration.cs "Code for the SqlBulkCopy method"
 [6]: http://msdn.microsoft.com/en-us/library/system.data.sqlclient.sqlbulkcopy.aspx "MSDN - SQLBulkCopy"