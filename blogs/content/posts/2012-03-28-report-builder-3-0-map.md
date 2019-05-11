---
title: Report Builder 3.0 – Map Wizard
author: Jes Borland
type: post
date: 2012-03-28T11:33:00+00:00
ID: 1582
excerpt: When SQL Server 2008 was introduced, it included the new spatial data types of geometry and geography. Report Builder 3.0 gave us the capability to add maps to reports, which can utilize those data types to visualize data.
url: /index.php/datamgmt/dbprogramming/report-builder-3-0-map/
views:
  - 9459
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
  - SSRS

---
This is part five of a series about Report Builder 3.0.

  * [Report Builder 3.0 – The Introduction][1]
  * [Report Builder 3.0 – Table or Matrix Wizard][2]
  * [Report Builder 3.0 – Chart Wizard][3]
  * [Report Builder 3.0 – Chart Types, Visualizations, and Properties][4]
  * [Report Builder 3.0 – Report Parts][5]

When SQL Server 2008 was introduced, it included the new spatial data types of [geometry and geography][6]. Report Builder 3.0 gave us the capability to add maps to reports, which can utilize those data types to visualize data.

The Map Wizard in Report Builder 3.0 provides a simple way for you to create a map or map layer, tying it to spatial data. To begin, you need to understand that a report needs spatial data, to show the points you're trying to display, and _can_ have analytical data, to show more information. I'll walk you through using the wizard to create a simple two-layer map that shows the sales (analytical data) per AdventureWorks store (spatial data) in the state of New York.

The Chart Wizard can be accessed in two ways. It will appear on the splash screen when you open Report Builder. It can also be found under File > New.

**The Wizard** 

_Choose a source of spatial data_ 

First, you need to choose where your data will come from.

  * Map gallery – These are predefined maps provided by Microsoft. They are .rdl files saved on your local computer. 
  * ESRI shapefile – These are a set of files that conform to the ESRI Shapefile spatial data format. 
  * SQL Server spatial query – You can reference the geometry or geography data types in a SQL Server database. (I will show this in a later example.) 

Select Map Gallery, expand USA, expand States by County, and select New York.

 

<p style="text-align: center;">
  <img src="/wp-content/uploads/blogs/DataMgmt/RB3 MapWiz1.JPG?mtime=1332901366" alt="" />
</p>

_Choose spatial data and map view options_ 

  * Map resolution – You can choose quality on a scale from best quality or smallest size. 
  * Embed map data in report – This option allows you to embed map elements that are used in the map layer. This is a default for the Map Gallery. 
  * Crop map as shown above – Pan and zoom the map using the controls on the left. Once you have it set, you can check this option for a default view of the map. 
  * Add a Bing Maps layer – This option adds in a Bing map tile which will provide a geographic background for the map view. 

 

<p style="text-align: center;">
  <img src="/wp-content/uploads/blogs/DataMgmt/RB3 MapWiz2.JPG?mtime=1332901367" alt="" />
</p>

_Choose map visualization_ 

  * Basic map – This displays locations – spatial data – only. 
  * Color analytical map – This displays data – analytical data – by color. 
  * Bubble map – This displays data – analytical data – using bubbles that vary in size, relative to the changes in the data. 

Choose Basic map and continue.

 

<p style="text-align: center;">
  <img src="/wp-content/uploads/blogs/DataMgmt/RB3 MapWiz3.JPG?mtime=1332901367" alt="" />
</p>

_Choose color theme and data visualization_ 

  * Theme – The theme determines the color scheme of the background, legend, and other parts surrounding the map. 
  * Single color map – This will make all regions of the map the same color. 
  * Display labels – This will show data points on the regions. 

Choose Single color map and Display labels. Select the label #COUNTYNAME.

 

<p style="text-align: center;">
  <img src="/wp-content/uploads/blogs/DataMgmt/RB3 MapWiz4.JPG?mtime=1332901368" alt="" />
</p>

When you click Finish, you'll be taken to the Report Builder main screen.

You can click on "Map Title" to change this.

When you click anywhere on the map, the Map Layers screen will pop up. Here, you can add a layer; delete a layer; access properties; shift the map view up, down, left, or right; and adjust the zoom level.

 

<p style="text-align: center;">
  <img src="/wp-content/uploads/blogs/DataMgmt/RB3 MapWiz5.JPG?mtime=1332901368" alt="" />
</p>

Running the report right now will result in a map of the state of New York, with the names of a few counties displayed. We need to add a dataset to tell the report what data to display.

I'm going to use AdventureWorks2008R2. You'll need to go to your database and create this view:

 

CREATE VIEW NYStoreSales AS SELECT ST.Name, ADDR.addressid, ADDR.City, SP.StateProvinceCode, SUM(SOH.TotalDue) AS TotalDue FROM person.Address AS ADDR INNER JOIN person.stateprovince AS SP ON SP.StateProvinceID = ADDR.StateProvinceID INNER JOIN Person.BusinessEntityAddress AS BEA ON BEA.AddressID = ADDR.AddressID INNER JOIN Sales.Store AS ST ON ST.BusinessEntityID = BEA.BusinessEntityID INNER JOIN Sales.Customer AS CUST ON CUST.StoreID = ST.BusinessEntityID INNER JOIN Sales.SalesOrderHeader AS SOH ON SOH.CustomerID = CUST.CustomerID WHERE SP.StateProvinceID=54 GROUP BY ST.Name, ADDR.addressid, ADDR.City, SP.StateProvinceCode ;

Double-click the map to bring up the Map Layers screen, and click the New layer wizard button.

 

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/RB3 MapWiz6.JPG?mtime=1332901940" alt="" />
</p>

_Choose a source of spatial data_

This time, choose SQL Server spatial query.

 

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/RB3 MapWiz7.JPG?mtime=1332901940" alt="" />
</p>

_Choose a dataset with SQL Server spatial data_

Select Add a new dataset with SQL Server spatial data and click Next.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/RB3 MapWiz8.JPG?mtime=1332901941" alt="" />
</p>

_Choose a connection to a SQL Server spatial data source_ 

I am going to use my pre-configured AdvWorks2008R2 dataset.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/RB3 MapWiz9.JPG?mtime=1332901941" alt="" />
</p>

_Design a query_

(I covered this screen in-depth in my [Table or Matrix Wizard][7] blog.) Select "Edit as Text" and add the following query:

SELECT NYS.Name, NYS.City, NYS.TotalDue, ADDR.SpatialLocation FROM NYStoreSales AS NYS INNER JOIN Person.Address AS ADDR ON ADDR.AddressID = NYS.addressid;

![][8]

_Choose spatial data and map view options_ 

  * Spatial field – Make sure this is set to the field in the query that contains the spatial data, SpatialLocation. 
  * Layer type – Choose Point, to display one point. A polygon would outline and area, and line could be used to mark a path. 

You won't "Embed map data" or "Add a Bing Maps layer" on this layer, in this example.

 

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/RB3 MapWiz17.JPG?mtime=1332903198" alt="" />
</p>

_Choose map visualization_ 

This time, choose Analytical Marker Map.

![][9]

_Choose the analytical dataset_ 

Pick the dataset you created earlier, which has the spatial data in it.

![][10]

_Choose color theme and data visualization_ 

  * "Use marker types" will add pin shapes at the points on the map. 
  * "Use marker sizes" will size the points differently, based on the data. 
  * "Use marker colors" will color the points on the map differently. 

Use marker types, based on City, and marker colors, for the SUM(Sales).

![][11]

Click Finish and you'll be taken back to the main screen. Now, you can see two layers have been added to the map.

![][12]

Click Run to view the results.

Here, you'll see a point for each city that has a store. Each point has a different marker (circle, square, pin, etc.) The color of the marker is going to vary based on the amount of sales – the lowest (Endicott) is white, the highest (Melville) is red.

![][13]

Maps are a great addition to Report Builder 3.0. You can easily align data from sales, customers, products, campaigns, or even the miles you've run each week with built-in maps. This takes reporting to a new level.

This was a very basic introduction – there is much more you can accomplish with maps. Check out these additional resources for more information!

<http://technet.microsoft.com/en-us/library/ee240845.aspx>

<http://technet.microsoft.com/en-us/library/ee240751.aspx>

<http://blogs.msdn.com/b/robertbruckner/archive/2009/08/11/rs-maps-with-spatial-data-and-bing-maps.aspx>

<http://blog.datainspirations.com/2010/05/11/sql-server-2008-r2-reporting-services-the-word-is-but-a-stage-t-sql-tuesday-006/>

 [1]: /index.php/All/?p=1634 "Report Builder 3.0 – The Introduction"
 [2]: /index.php/All/?p=1635 "Report Builder 3.0 - Table or Matrix Wizard"
 [3]: /index.php/All/?p=1647 "Report Builder 3.0 - Chart Wizard"
 [4]: /index.php/All/?p=1653
 [5]: /index.php/DataMgmt/ssrs/report-builder-3-0-report
 [6]: http://msdn.microsoft.com/en-us/library/bb964711.aspx
 [7]: /index.php/DataMgmt/ssrs/report-builder-3-0-table
 [8]: /wp-content/uploads/users/grrlgeek/RB3 MapWiz11.JPG?mtime=1332902571
 [9]: /wp-content/uploads/users/grrlgeek/RB3 MapWiz12.JPG?mtime=1332902571
 [10]: /wp-content/uploads/users/grrlgeek/RB3 MapWiz13.JPG?mtime=1332902572
 [11]: /wp-content/uploads/users/grrlgeek/RB3 MapWiz14.JPG?mtime=1332902573
 [12]: /wp-content/uploads/users/grrlgeek/RB3 MapWiz15.JPG?mtime=1332902573
 [13]: /wp-content/uploads/users/grrlgeek/RB3 MapWiz16.JPG?mtime=1332902573