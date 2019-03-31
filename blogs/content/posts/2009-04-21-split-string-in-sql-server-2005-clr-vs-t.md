---
title: 'Split string in SQL Server 2005+  CLR vs. T-SQL'
author: Ted Krueger (onpnt)
type: post
date: 2009-04-21T22:32:39+00:00
url: /index.php/datamgmt/dbprogramming/split-string-in-sql-server-2005-clr-vs-t/
views:
  - 47312
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server

---
I&#8217;m editing in a link to Adam Machanic&#8217;s blog on this. In the comments on this topic here you will see there are imperfections found in my methods. Reading Adam&#8217;s blog shows this in more detail.
  
http://sqlblog.com/blogs/adam_machanic/archive/2009/04/26/faster-more-scalable-sqlclr-string-splitting.aspx 

Thanks Adam!

I wrote this short CLR split function awhile back based on a few other articles I read when 2005 was released. I decided to play with it today and see if I could put it with the SQL split solutions. 

Let&#8217;s get the basics out of the way on SQL CLR. SQL CLR is only good once it&#8217;s in memory. The CLR function split basically won over the T-SQL split functions after it was cached. This is a critical variable to consider when thinking CLR vs. T-SQL options on coding. If you are doing heavy manipulation of data and heavy math, CLR will typically help you, but you should be very careful with CLR and memory management. You can run your server resources out and literally stop functionality. I highly recommend reading MrDenny&#8217;s blog on CLR [here][1]. Denny touches on important topics on when to use CLR and why you shouldn&#8217;t. After that, look into how SQL Server 32bit, 32bit with AWE and 64bit versions handle memory. Each handles memory differently. AWE enalbed instances will probably be the one that will cause you more headaches then the rest. I had severe memory issues a few months ago on a production database server that forced restarts nightly until I fixed the problem. I analyzed the problem and it came to be several factors that caused it and SQL CLR memory was one of those factors. Here is my chance to thank mrdenny and ptheriault again for the assisatnce on that strange problem.

I went out and google&#8217;d &#8220;fast split function t-sql&#8221;. Found a few and tested them against the CLR split method. I found a dozen or so split functions that looked good. I still went with a numbers table one after testing them out next to each other. Here is one of the functions I used. If you have a better one, post it in the comments and I can edit the post. 

<pre>ALTER FUNCTION [dbo].[Split] ( 
@List varchar(7998), --The delimited list 
@Del char(1) = ',' --The delimiter 
) 
RETURNS @T TABLE (Item varchar(7998)) 
AS 
BEGIN 
DECLARE @WrappedList varchar(8000), @MaxItems int 
SELECT @WrappedList = @Del + @List + @Del, @MaxItems = LEN(@List) 

INSERT INTO @T (Item) 
SELECT SUBSTRING(@WrappedList, Number + 1, CHARINDEX(@Del, @WrappedList, Number + 1) - Number - 1) 
FROM dbo.Numbers n 
WHERE n.Number &lt;= LEN(@WrappedList) - 1 
AND SUBSTRING(@WrappedList, n.Number, 1) = @Del 

RETURN 
END</pre>

Here is my CLR split

<pre>using System;
using System.Data;
using System.Collections;
using System.Data.SqlClient;
using System.Data.SqlTypes;
using Microsoft.SqlServer.Server;

public partial class UserDefinedFunctions
{
    [SqlFunction(Name = "CLR_Split",
    FillRowMethodName = "FillRow", 
    TableDefinition = "id nvarchar(10)")]

    public static IEnumerable SqlArray(SqlString str, SqlChars delimiter)
    {
        if (delimiter.Length == 0)
            return new string[1] { str.Value };
        return str.Value.Split(delimiter[0]);
    }

    public static void FillRow(object row, out SqlString str)
    {
        str = new SqlString((string)row);
    }
};</pre>

I loaded a text file with a huge amount of delimited data to really get a gauge on time this would take. The string is basically, &#8220;data%data%data%data%data&#8221; and on. Around 600 indexes. I restarted my local instance of SQL Server 2005 that I did these on to ensure you can see CLR before cache and after. 

So on a fresh restart you can see by checking type MEMORYCLERK\_SQLCLR from dm\_os\_memory\_clerks that we are clear

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//clr_clerk_1.gif" alt="" title="" width="1439" height="63" />
</div>

After execution of the CLR function you can see the differences

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//clr_clerk_2.gif" alt="" title="" width="1187" height="39" />
</div>

Call these functions as

<pre>Declare @bigdamnvar varchar(max)
Set @bigdamnvar = (Select * From OpenRowSet ('MSDASQL', 'Driver={Microsoft Text Driver (*.txt; *.csv)};DBQ=C:', 'SELECT * from Data.txt'))
Select * From dbo.CLR_Split(@bigdamnvar, '%')</pre>

Below you can see first execution and then how quickly performance picks up on the second cached plan

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//split_clr.gif" alt="" title="" width="496" height="368" />
</div>

and

<pre>Declare @bigdamnvar varchar(max)
Set @bigdamnvar = (Select * From OpenRowSet ('MSDASQL', 'Driver={Microsoft Text Driver (*.txt; *.csv)};DBQ=C:', 'SELECT * from Data.txt'))
Select * From dbo.[Split](@bigdamnvar, '%')</pre>

I executed this a few times to get it in cache as well. The odd increase was the server working on something else. I validated that so ignore it.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//split_tsql.gif" alt="" title="" width="769" height="372" />
</div>

You may notice the bytes recieved from the server are different and SQL CLR is much heavier. That is something to keep in mind. SQL will always be light compared to SQL CLR. That is my experience in using the two side by side.

The differences are small but if the task you intend to perform is something cached typically, keep CLR in mind. Mostly when it come to complicated tasks that may be left to other languages you have available.

 [1]: http://itknowledgeexchange.techtarget.com/sql-server/sql-clr-the-what-when-why-and-how/