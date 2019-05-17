---
title: SQL Server Distance Calculation Option 3 – Using the CLR
author: Alex Ullrich
type: post
date: 2009-02-18T00:42:00+00:00
ID: 328
url: /index.php/datamgmt/dbprogramming/sql-server-distance-calculation-option-3/
views:
  - 14453
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
tags:
  - clr
  - proximity
  - search
  - sql server

---
In [2005- Version][1] and [2008 Version][2] George and Denis have already shown a couple of great ways to calculate distance between two pairs of latitude/longitude coordinates in SQL Server. In SQL 2005 + there is a third option (Well, it was the second option until 2008 introduced the new Geography data type). SQL 2005 was the first version of SQL Server to offer CLR integration – giving developers the ability to write their own functions and stored procs as .NET assemblies. In addition to opening a whole new world of possibilities to SQL developers, when used wisely these functions/procs will in some cases outperform standard T-SQL, especially when it comes to math and string manipulation.

So, lets get down to business. First thing to do is create a class library to hold your functions. You can do this in C# or VB.net, I won't judge. But I will use C#.

```csharp
using System;
using System.Data;
using System.Data.SqlClient;
using System.Data.SqlTypes;
using Microsoft.SqlServer.Server;

public partial class UserDefinedFunctions
{
    [Microsoft.SqlServer.Server.SqlFunction]
    public static double clrDistCalc(double long1, double lat1, double long2, double lat2)
    {
        double EarthCircMeters = 40074199.82569710;
        double DeltaX;
        double DeltaY;
        double DeltaXMeters;
        double DeltaYMeters;
        double MetersPerDegreeLong;
        double CenterY;

        //calculate distance
        DeltaX = Math.Abs(long2 - long1);
        DeltaY = Math.Abs(lat2 - lat1);
        CenterY = (lat2 + lat1)/2;
        MetersPerDegreeLong = (Math.Cos(CenterY * (3.14159265/180)) * EarthCircMeters)/360;
        DeltaXMeters = DeltaX * MetersPerDegreeLong;
        DeltaYMeters = DeltaY * 111113.519;

        return Math.Sqrt(Math.Pow(DeltaXMeters, 2) + Math.Pow(DeltaYMeters, 2))/1609.344;
    }
}
```

I've never tried to optimize this (it is pretty fast as is), but I am sure it is easily doable. The important thing to notice here is the additional namespace directives (past System). These allow you to use SQL Server types, Identify your function as a SQL Server function, and loads of other fun stuff. I don't actually use any of the SQL types in this function, but they do work well in most cases. Its' easy to see the SqlFunction attribute applied to the method.

Once this is done, compile that sucker! The trickiest part here is really deploying it to your server, but even that is simple, just a lot of steps. First, you'll need to put the file in a location where the server can see it. On the server would be a nice easy place. Now we need to create an assembly in SQL Server for it. This is simple too:

```sql
CREATE ASSEMBLY DistanceCalculations FROM 'C:DistanceCalculationLibrary.dll'
```

Ok, we're getting close. Once this assembly is created, you need to enable the CLR (it is disabled by default).

```sql
exec sp_configure 'clr enabled',1
reconfigure
```

And finally, create our SQL Server udf referencing the assembly. 

```sql
CREATE FUNCTION [dbo].[clrDistCalc](@long1 [float], @lat1 [float], @long2 [float], @lat2 [float])
RETURNS [float] WITH EXECUTE AS CALLER
AS 
EXTERNAL NAME [DistanceCalculations].[UserDefinedFunctions].[clrDistCalc]
```
You can test it by writing some queries against the same table used for Denis' example (or George's). Here's an example using Denis' data:

```sql
SELECT h.*
   FROM zipcodes g
   INNER JOIN zipcodes h ON g.zipcode <> h.zipcode
   AND g.zipcode = '10028'
   AND h.zipcode <> '10028'
   WHERE dbo.clrDistCalc(g.Longitude, g.Latitude, h.Longitude, h.Latitude) <= (20 * 1609.344)
```

If I know those guys like I think I do, the indexes and everything will be sufficient! 

That's really all there is to it. These CLR functions are an **EXTREMELY** powerful tool for any developer's kit. Math calculations are just scratching the surface of the great uses for this capability. It is important to use them judiciously though, as with most powerful tools you can seriously injure yourself if not careful! I'm not sure I've determined where the line is yet, but I would not even attempt using the CLR unless I was having performance issues with frequently used calculations (or they were frequently used enough that I could see the problem coming) or if I needed access to the filesystem or other resources that are awkward to access from straight T-SQL. As developers we're always going to need to make choices like this, and no amount of blog posts will make them for us. So like all things, just use it wisely and you'll be fine! 

In the future we'll put all three to the test, and do a fourth post detailing the results. At least I hope we will, the idea of doing just that is the whole reason I blogged about this again!

\*** **If you have a SQL Server related question try our [Microsoft SQL Server Programming][3] forum or our [Microsoft SQL Server Admin][4] forum**<ins></ins>

 [1]: /index.php/DataMgmt/DataDesign/sql-server-zipcode-latitude-longitude-pr
 [2]: /index.php/DataMgmt/DataDesign/sql-server-2008-proximity-search-with-th
 [3]: http://forum.lessthandot.com/viewforum.php?f=17
 [4]: http://forum.lessthandot.com/viewforum.php?f=22