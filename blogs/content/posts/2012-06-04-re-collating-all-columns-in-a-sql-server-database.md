---
title: Re-Collating All Columns in a SQL Server Database
author: Alex Ullrich
type: post
date: -001-11-30T00:00:00+00:00
excerpt: "At my last job, after getting a few support tickets from customers using case-sensitive collations we decided to change over to case-sensitive collations in our development environment.  Changing the database collation wasn't enough though - this just c&hellip;"
draft: true
url: /?p=1309
categories:
  - Data Modelling and Design
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
At my last job, after getting a few support tickets from customers using case-sensitive collations we decided to change over to case-sensitive collations in our development environment. Changing the database collation wasn&#8217;t enough though &#8211; this just created more problems, because new columns would be created with the new case-sensitive collation while old columns retain their original collation. This can cause issues when doing just about anything with a pair of mismatched columns, including equality comparison and union operations.

Recollating ended up being more trouble than expected, because the collation affects indexes as well as column definitions. So what we expected to be just a series of ALTER TABLE commands ended up being significantly more complicated. For each mismatched column we needed to perform the following operations:

  * Store Information about Affected Indexes
  * Drop Indexes including Column
  * Alter Column Collation
  * Re-Create Indexes

Because of the time this can take and the cost of regenerating statistics, recollation in a production environment would introduce additional concerns. After getting sufficiently frustrated with manually fixing each column as we discovered issues, a colleague and I wrote a script to handle this for us. Figured it would be worth sharing here both because it can be useful, and it provides a little bit of a glimpse into SQL Server&#8217;s system tables.

We started with a script that Lowell Izaguirre contributed at [SQL Server Central (requires login)][1] that scripts all indexes as CREATE STATEMENTS. All we needed to do here was tweak the end output to populate a table full of &#8220;drop index&#8221; statements as well. From there we were able to loop over all the indexes in the database, dropping each one. With a few modifications it would be possible to limit what is dropped/recreated further but we did not need to do so.

From there we just needed to populate a table variable with information about the columns to recollate. We can limit this to the columns that need recollation very easily, by checking the COLLATION\_NAME column value in INFORMATION\_SCHEMA.COLUMNS.

[DatabaseRecollatorMcJawn.txt][2]

 [1]: http://www.sqlservercentral.com/scripts/Indexing/31652/
 [2]: /wp-content/uploads/blogs/All/recollating-all-columns/DatabaseRecollatorMcJawn.txt?mtime=1315590758