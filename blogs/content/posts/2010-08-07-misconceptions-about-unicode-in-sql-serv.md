---
title: Misconceptions about unicode in SQL Server
author: SQLDenis
type: post
date: 2010-08-07T16:17:37+00:00
url: /index.php/datamgmt/datadesign/misconceptions-about-unicode-in-sql-serv/
views:
  - 7569
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - datalength
  - len
  - myth
  - nvarchar
  - sql server
  - sql server 2005
  - sql server 2008
  - unicode

---
I was browsing around last night on the internet and a person wanted to know what data type to use to store a course name. Here is an answer he got:

> I would recommend nvarchar or varchar for the name. Use nvarchar if you anticipate there ever being a point in the future that you will need to support foreign languages that might contain unicode characters that are not supported by varchar. **I would look at the longest name I anticipate needing, double it&#8217;s length and then round up to the nearest 50. So if &#8220;Introduction to Partial Differential Equations&#8221; at 48 then I would make it varchar(100)**

Okay so that part in bold is completely wrong, Nvarchar uses 2 bytes per character for storage but you do not specify that. So if you want to store the character &#25991; you need nchar(1) not nchar(2).

Run this and you will see the character &#25991; in the output
  

** 

<pre>declare @n nchar(1)
set @n = N'文' 

select @n</pre>

</strong>

_And no, there is no mistake, the reason I didn&#8217;t use the codeblock that I use for the rest of this post is that the &#25991; character gets changed to `&#25991 so the SQL would be incorrect`_

Also be aware that you need to have the N in front of the string, this tells SQL Server that it has to treat it as unicode. If you do this &#8216;&#25991;&#8217; instead of N&#8217;&#25991;&#8217; you will get a question mark in the output.

What if you wanted to store over 4000 characters in a nvarchar? Varchar goes up to 8000 characters but nvarchar only goes up to 4000 characters, take a look and run the statement below.

<pre>DECLARE @n NVARCHAR(4001)</pre>

Below is the error that you will get.
  
_Msg 2717, Level 16, State 2, Line 1
  
The size (4001) given to the parameter &#8216;@n&#8217; exceeds the maximum allowed (4000)._

But there is hope, you can use NVARCHAR(max) to store up to 2GB of data, so about a billion characters or so

<pre>DECLARE @n NVARCHAR(max)</pre>

One more thing to be aware of when using unicode is that LEN will give you the number of characters but DATALENGTH will give you the storage required to store the character(s)

Run this

<pre>SELECT LEN(N'1'),DATALENGTH(N'1')</pre>

LEN returns 1 and DATALENGTH returns 2. To learn more about LEN and DATALENGTH, take a look at the [The differences between LEN and DATALENGTH in SQL Server][1] post I wrote a while back.

That is all for this short weekend post.

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][2] forum or our [Microsoft SQL Server Admin][3] forum**<ins></ins>

 [1]: /index.php/DataMgmt/DBProgramming/the-differences-between-len-and-dataleng
 [2]: http://forum.ltd.local/viewforum.php?f=17
 [3]: http://forum.ltd.local/viewforum.php?f=22