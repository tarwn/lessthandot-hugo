---
title: Optimizing with indexing where commonly overlooked
author: Ted Krueger (onpnt)
type: post
date: 2009-03-22T22:27:11+00:00
ID: 361
url: /index.php/datamgmt/datadesign/index-trick-convert-index-scan-to-seek/
views:
  - 3443
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Microsoft SQL Server

---
This is a quick and probably extremely basic trick to get rid of a scan when optimizing your code. Getting a index scan to a index seek can be a great increase in speed. Several places in normal day-to-day coding you will use Count(*), Max(col) etc.. to get data in order to return the required data set. Although this tip will be based on the data you are working on and the tables structure, it can be used several times to remove scans where you possibly were overlooking them.

Take a look at a thin test table

sql
SET ANSI_NULLS ON
GO

SET QUOTED_IDENTIFIER ON
GO

CREATE TABLE [dbo].[test_scan](
	[Col1_Data] [int] NULL,
	[MyID] [int] IDENTITY(1,1) NOT NULL,
 CONSTRAINT [PK_test_scan] PRIMARY KEY CLUSTERED 
(
	[MyID] ASC
)WITH (PAD_INDEX  = OFF, STATISTICS_NORECOMPUTE  = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS  = ON, ALLOW_PAGE_LOCKS  = ON) ON [PRIMARY]
) ON [PRIMARY]

GO
```
We now have a column that we at one point in time may want to loop through or grab the count of in order to populate a variable or simply return the value for purposes of reporting.

Let&#8217;s look at this query

sql
Declare @cnt_test bigint
Set @cnt_test = (Select count(*) From dbo.test_scan)
```
The resulting plan will show an index scan on our index PK\_test\_scan. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//scan_trick.gif" alt="" title="" width="982" height="126" />
</div>

That is something we&#8217;d like to get out of there. Really a seek will come from us being a bit more graphic in what we want from the tables. Saying that is the answer to the problem though. If you have an identity column and you haven&#8217;t gone into the negative range of the values to utilize space then you already know the column will consist of data greater than 0. Remember this is on an IDENTITY(1,1) column. So we have our answer because we know to get a seek out of the query we need to get the count of the rows in that table is simply 

sql
Select count(*) From dbo.test_scan where MyID >= 0
```
Running this and showing the plan we can see our scan now is doing a seek on PK\_test\_scan. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//scan_trick2.gif" alt="" title="" width="983" height="127" />
</div>

So now if we perform a 

sql
Declare @cnt_test bigint
Set @cnt_test = (Select count(*) From dbo.test_scan where MyID >= 0)
```
We&#8217;ve just optimized that little portion of the big picture that is commonly overlooked. Remember that this is heavily based off your tables data and be careful not to return incorrect results by trying to force this into places it really does not fit.