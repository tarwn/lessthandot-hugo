---
title: Compressing Data with Uniqueidentifier columns
author: Ted Krueger (onpnt)
type: post
date: 2011-05-03T10:52:00+00:00
ID: 1157
excerpt: 'I was running some tests on compression for row and page level, and noticed something interesting.  All tables that contained a uniqueidentifier column actually resulted in compressing sample sizes larger than the uncompressed size on the respected inde&hellip;'
url: /index.php/datamgmt/dbadmin/compressing-data-with-uniqueidentifier-columns/
views:
  - 7561
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
I was running some tests on compression for row and page level, and noticed something interesting.  All tables that contained a uniqueidentifier column actually resulted in compressing sample sizes larger than the uncompressed size on the respected indexes that contained the uniqueidentifier.  This made sense given the precision of a uniqueidentifier but wanted to test that and also make sure it was in writing before stating the fact.

**Estimate Compression ROW and PAGE level**

To return a sampling of the compression result, use the procedure sp\_estimate\_data\_compression\_savings.  The procedure is very useful but it is an “_estimation_” tool. 

To run a test, create the following table and then execute the estimation procedure on both samplings for ROW and PAGE level compression.

sql
create table compress_test (id uniqueidentifier default newid())
go
insert into compress_test default values;
go 10000
EXEC sp_estimate_data_compression_savings 'dbo', 'compress_test', NULL, NULL, 'PAGE' ;
GO
```

With compressing a table with one column, the results would be thought to compress greatly.  In this case they do not though.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-51.png?mtime=1304392780"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-51.png?mtime=1304392780" width="624" height="118" /></a>
</div>

**Hold on...let's talk**

Before going over the results, note: compression will vary slightly based on the data types that are in the table.  There are many data types that cannot be compressed and some that can be greatly impacted by enabling either ROW or PAGE.  In most cases, ROW and PAGE level compression will also have drastic differences.  A perfect example of a high impact compression can make is on CHAR data types.  The benefit of the compression on CHAR is the removal of the trailing space. 

Compression may sound like a great thing and in some cases, it is.  There are test cases that should be taken before actually enabling compression at the ROW or PAGE level though.  Compression increases CPU utilization as the most negatively impacted resource.  The trade-off in I/O performance in cases can greatly outweigh that CPU increase. 

Back to the uniqueidentifier; Books Online has a table that says exactly the [cost savings of data types][1] with compression.  In the listing, uniqueidentifier is listed with no effect from compression.  This was acceptable based on the case that other data types in the table may benefit greatly still. 

After looking into these results, the Books Online article that describes the [procedures for estimating compression rates][2] had the answer.

_If the results of running sp\_estimate\_data\_compression\_savings indicate that the table will grow, this means that many rows in the table use almost the whole precision of the data types, and the addition of the small overhead needed for the compressed format is more than the savings from compression. In this rare case, do not enable compression._

With uniqueidentifier data types, this is the case.  Compression at this stage will take more to perform. 

This doesn't mean NOT to enable compression by default because you see a uniqueidentifier column in a table.  In fact, let's test the next script.

sql
create table compress_test_char (id uniqueidentifier default newid(), CHAR_COL CHAR(8000) default 'Take all this space, plus some')
go
insert into compress_test_char default values;
go 10000

EXEC sp_estimate_data_compression_savings 'dbo', 'compress_test_char', NULL, NULL, 'PAGE' ;
GO
```

This table benefits extremely well from using PAGE compression. 

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-52.png?mtime=1304392780"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-52.png?mtime=1304392780" width="624" height="140" /></a>
</div>

This reason for this is the effects from the CHAR data type and the padding removal. 

**Double Check Before Enabling**

As stated before, each table's results will be unique when the estimation is complete.  The data types in them will cause them to fail or succeed on being benefiting from compression.  A combination of data types that benefit from compression and those that don't will make or break the case.

A perfect example to leave you with is the Sales.SalesOrderDetail table in AdventureWorks.  I searched AW to find a table with a combination of data types that probably would not be beneficial from enabling compression.  SalesOrderDetail was such a case just on the fence.

sql
EXEC sp_estimate_data_compression_savings 'Sales', 'SalesOrderDetail', NULL, NULL, 'PAGE' ;
GO
```
<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-53.png?mtime=1304392781"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-53.png?mtime=1304392781" width="624" height="118" /></a>
</div>

We can see the true impact is on the first index but there is a loss on the second index.  Looking into the second index it is shown as the AK\_SalesOrderDetail\_rowguid index.  This index is the uniqueidentfier in the table.  Of course, the compression of the primary key and the slightcompression of the product ID index accumulate to savings still.  That was the why the “fence” statement earlier. 

Make sure you test your compression estimated results before enabling it on your tables.  You may actually hurt yourself if certain data types are in the table and there are no true benefiting data types existing.

**Resources**

[sp\_estimate\_data\_compression\_savings][2]

[Creating Compressed Tables and Indexes][3]

[Page Compression Implementation][4]
  
[Row Compression Implementation][1]

 [1]: http://msdn.microsoft.com/en-us/library/cc280576.aspx
 [2]: http://msdn.microsoft.com/en-us/library/cc280574.aspx
 [3]: http://msdn.microsoft.com/en-us/library/cc280449.aspx
 [4]: http://msdn.microsoft.com/en-us/library/cc280464.aspx