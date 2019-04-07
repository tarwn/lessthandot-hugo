---
title: How To Convert IP Addresses Between Bigint and Varchar
author: SQLDenis
type: post
date: 2008-10-05T16:06:53+00:00
ID: 161
excerpt: |
  Before we start with code let us take a sample IP address, does 127.0.0.1 look familiar? Yes that is your local IP address.
  
  Here it is in decimal and binary
  127 0 0 1
  01111111 00000000 00000000 00000001
  
  Now to convert, you would take the first v&hellip;
url: /index.php/datamgmt/datadesign/how-to-convert-ip-addresses-between-bigi/
views:
  - 27600
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - how to
  - sql
  - sql server 2008
  - tip

---
Before we start with code let us take a sample IP address, does 127.0.0.1 look familiar? Yes that is your local IP address.

Here it is in decimal and binary
  
127 0 0 1
  
01111111 00000000 00000000 00000001

Now to convert, you would take the first value,
  
add the second value + 256
  
add the third value + (256 * 256) = 65536
  
add the fourth value + (256 \* 256 \* 256) =16777216

So in our case the select would be

sql
select
1 +
0 * 256 +
0 * 65536 +
127 * 16777216
```

which is
  
2130706433

So to convert from IP Adress to integer is very simple, you use PARSENAME to split it up and do the math. Here is the function.

sql
CREATE FUNCTION dbo.IPAddressToInteger (@IP AS varchar(15))
RETURNS bigint
AS
BEGIN
 RETURN (CONVERT(bigint, PARSENAME(@IP,1)) +
         CONVERT(bigint, PARSENAME(@IP,2)) * 256 +
         CONVERT(bigint, PARSENAME(@IP,3)) * 65536 +
         CONVERT(bigint, PARSENAME(@IP,4)) * 16777216)

END
GO
```

But how do you get 127.0.0.1 out of 2130706433?
  
It is the reversed of what we did before (surprise) so instead of multiplying we will be dividing
  
Here is the funcion

sql
CREATE FUNCTION dbo.IntegerToIPAddress (@IP AS bigint)
RETURNS varchar(15)
AS
BEGIN
 DECLARE @Octet1 bigint
 DECLARE @Octet2 tinyint
 DECLARE @Octet3 tinyint
 DECLARE @Octet4 tinyint
 DECLARE @RestOfIP bigint

 SET @Octet1 = @IP / 16777216
 SET @RestOfIP = @IP - (@Octet1 * 16777216)
 SET @Octet2 = @RestOfIP / 65536
 SET @RestOfIP = @RestOfIP - (@Octet2 * 65536)
 SET @Octet3 = @RestOfIP / 256
 SET @Octet4 = @RestOfIP - (@Octet3 * 256)

 RETURN(CONVERT(varchar, @Octet1) + '.' +
        CONVERT(varchar, @Octet2) + '.' +
        CONVERT(varchar, @Octet3) + '.' +
        CONVERT(varchar, @Octet4))
END
```

Now let&#8217;s try this out, first run this

sql
select dbo.IPAddressToInteger('127.0.0.1')
```

That returns 2130706433
  
Now run this

sql
select dbo.IntegerToIPAddress(2130706433)
```

That returns 127.0.0.1

Thanks to [K. Brian Kelley][1] for the inspiration for this post, you can also check http://www.truthsolutions.com/ to see some of his books

And also check out the related [Order IP Addresses][2] wiki article which I wrote a while ago

 [1]: http://www.sqlservercentral.com/blogs/brian_kelley/default.aspx
 [2]: http://wiki.ltd.local/index.php/Order_IP_Addresses