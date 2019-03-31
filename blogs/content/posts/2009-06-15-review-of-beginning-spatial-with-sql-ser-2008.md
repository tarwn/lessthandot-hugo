---
title: Review Of Beginning Spatial With SQL Server 2008
author: SQLDenis
type: post
date: 2009-06-15T17:54:16+00:00
url: /index.php/datamgmt/datadesign/review-of-beginning-spatial-with-sql-ser-2008/
views:
  - 5690
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - geography
  - geometry
  - gis
  - spatial
  - spatial data
  - sql server
  - sql server 2008

---
I will just start of by saying that [Beginning Spatial With SQL Server 2008][1] written by Alastair Aitchison is a truly awesome book. Although I have tinkered a little with the new geospatial capabilities in the [SQL Server 2008 Proximity Search With The Geography Data Type][2] blog post I did not really understand or know that there were so many nuances and things that you had to be careful about.
  
Did you know that there are different map projections? This book will show you the differences between the Hammer-Aitoff map projection, the Mercator map projection and the equirectangular map projection. You will also learn what the different spatial reference systems that SQL server 2008 supports are. 

If you are a SQL server developer and you have to incorporate some spatial data on your website or perhaps on your intranet then this is the book for you. The book is written in a casual style, things that might seem complicated at first are explained thoroughly over several pages. There are many images in this book (we all know that an image is a thousand words) which really help explain concepts. There are links in this book that point to great resources. Here is what is covered in the book

_This books starts out by defining what spatial information is. Explained is the difference between Points, LineStrings and Polygons, how to choose the right Geometry and understanding Interiors, Exteriors and Boundaries. The different map projections are explained with nice clear images. </p> 

In chapter two you will learn the difference between the geography and the geometry data type and when to choose one over the other. Converting between datatypes and choosing the right spatial datatype is also covered. Finally you will learn how spatial data is actually stored and how to enable spatial data for your tables

In chapter three you will see some spatial data examples in T-SQL and .NET and how to combine T-SQL and .NET CLR methods

Chapter four shows you how to create objects like points, linsetrings and geometry collections from Well-Known-Text, Well-Known-Binary and Geography Markup Language

Using Virtual Earth with spatial data is covered in chapter five

Chapter six shows you how to import spatial data and the use of third party conversion tools

In chapter seven Geocoding is explained

Chapter eight covers syndicating spatial data by means of the GeoRSS format. Both consuming and serving the GeoRSS feed are discussed

Google maps and spatial data are shown in chapter nine. This includes Ajax, JavaScript and the Stored Procedure that will return the data

Chapter ten is about visualizing query results in SQL Server Management Studio; this covers the Equirectangular, Mercator, Robinson and Bonne projections

Chapter eleven examines all the properties of spatial data objects. Some things included arefinding the Centroid of a gemotry Polygon and isolating the Exterior Ring of a Geometry Polygon

Chapter twelve is all about modifying Spatial Objects

Chapter thirteen is about testing spatial relationships; one example is whether two Geometries are disjoint

Finally in chapter fourteen indexing is covered. Creating and optimizing and index are covered</em>

I highly recommend this book if you want to learn about spatial data in SQL Server 2008. You will not be disappointed! The book has 14 chapters and a total of 425 pages.

 [1]: http://www.amazon.com/gp/product/1430218290?ie=UTF8&tag=sql08-20&linkCode=as2&camp=1789&creative=390957&creativeASIN=1430218290
 [2]: /index.php/DataMgmt/DataDesign/sql-server-2008-proximity-search-with-th