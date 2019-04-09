---
title: Distance Calculation Showdown
author: Alex Ullrich
type: post
date: 2009-04-20T11:28:42+00:00
ID: 376
url: /index.php/datamgmt/dbprogramming/distance-calculation-showdown/
views:
  - 9099
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server

---
A while back George, Denis and I did a series of posts on calculating distance between sets of latitude/longitude coordinates. Those posts can be found here.

[Part 1: T-SQL][1]
  
[Part 2: Geospatial Data Type (2008)][2]
  
[Part 3: CLR (2005 +)][3]

At the time I promised to run some tests. I had an idea how it would turn out at that point, but wanted to make my testing a bit more thorough. So, I've got the three functions that were created from following along with those blog entries. I then created a new stored proc for each method. For example (this is the TSQL version):

sql
CREATE proc [dbo].[ClosestZips_TSQL] (@ZipCode char(5), @Quantity int)
as

begin
set nocount on

SELECT top ( @Quantity ) h.ZipCode
	, h.City
	, h.StateAbbreviation
	, h.Latitude
	, h.Longitude
	, dbo.clrDistCalc(h.Longitude, h.Latitude, g.Longitude, g.Latitude) as Distance 
FROM zipcodes g
JOIN zipcodes h ON g.zipcode <> h.zipcode
	AND g.ZipCode = @ZipCode
WHERE dbo.tsqlDistCalc(h.Longitude, h.Latitude, g.Longitude, g.Latitude)<=(200 * 1609.344)
ORDER BY dbo.tsqlDistCalc(h.Longitude, h.Latitude, g.Longitude, g.Latitude)

end
```

Then, I ran these procs for my zip code, for a varying number of results. The number of results didn't turn out to matter, so I've left that out. This table contains the results:

<table border="1" cellpadding="2" align="center">
  <tr>
    <th>
      Method
    </th>
    
    <th>
      Average Time (10 Runs)
    </th>
  </tr>
  
  <tr>
    <td>
      T-SQL
    </td>
    
    <td>
      2,075 ms
    </td>
  </tr>
  
  <tr>
    <td>
      GEOSPATIAL
    </td>
    
    <td>
      1,272 ms
    </td>
  </tr>
  
  <tr>
    <td>
      CLR
    </td>
    
    <td>
      600 ms
    </td>
  </tr>
</table>

These times were on a pretty weak laptop, at least by software developer's standards. I ran the test a whole bunch of times, and these were typical times (but in the low end of the spectrum I observed). I am sure times on a better machine would be much faster, and I would love to hear other people's results.

So, the CLR was the winner in this test, but there are some things to consider besides speed. One is its availability. Of course it is not available on SQL 2000 or earlier, but there are other factors in play as well. For example, on my shared web host I cannot run CLR code in the database :-/. Some organizations may not allow CLR integration code on their servers as a rule either. A big advantage you have with the other two is that if you have a SQL Server with the requisite version at your disposal, you can use them. 

Another thing to consider is what else you can do with the data. Using the CLR and T-SQL approaches, the data is stored as raw coordinate pairs. I haven't confirmed this, but they seem much quicker to write to as well, so that should be taken into consideration if your app will need to do a lot of writing (not likely for a “Closest Store” type of thing).

Storing the raw coordinate pairs will also allow you to do some cool things to limit the size of your query, that can yield tremendous performance gains. For example, George showed me a trick once where you can first use the pythagorean theorem to approximate distance, and use this to create a bounding box. You then limit the number of pairs you need to run the “real” calculation on and really speed things up. But that might be a topic for another post.

I've tried to cover the ups and downs of these methods the best that I could. I'd love to hear about anything I missed. Have fun!

\*** **Got a SQL Server question? Check out our [Microsoft SQL Server Programming][4] forum or our [Microsoft SQL Server Admin][5] forum**

 [1]: /index.php/DataMgmt/DataDesign/sql-server-zipcode-latitude-longitude-pr
 [2]: /index.php/DataMgmt/DataDesign/sql-server-2008-proximity-search-with-th
 [3]: /index.php/DataMgmt/DBProgramming/sql-server-distance-calculation-option-3
 [4]: http://forum.ltd.local/viewforum.php?f=17
 [5]: http://forum.ltd.local/viewforum.php?f=22