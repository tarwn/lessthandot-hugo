---
title: SQL Server 2008 Proximity Search With The Geography Data Type
author: SQLDenis
type: post
date: 2009-02-11T18:27:31+00:00
ID: 324
excerpt: |
  George and I decided to show you how you can do simple radius searches based on Zip Codes, he did the SQL 2005 and before version here:   SQL Server Zipcode Latitude/Longitude proximity distance search and I will do the SQL Server 2008 version.
  
  The f&hellip;
url: /index.php/datamgmt/datadesign/sql-server-2008-proximity-search-with-th/
views:
  - 87740
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Microsoft SQL Server
tags:
  - geography
  - geomapping
  - spatial data
  - sql server 2008

---
George and I decided to show you how you can do simple radius searches based on Zip Codes, he did the SQL 2005 and before version here: [SQL Server Zipcode Latitude/Longitude proximity distance search][1] and I will do the SQL Server 2008 version.

The first thing we need to do is load our data. There are various sources for this data, ranging from free to expensive monthly subscriptions. One source of free data is: http://download.geonames.org/export/zip/ make sure to grab the US.ZIP file for this demo. Unrar/Unzip the file and you will have a US.txt file.

Once you have downloaded your data, the next step is to import it in to your database. You can use the following script to do it.

```sql
CREATE TABLE ZipCodesTemp(
    [Country] [VARCHAR](2) NULL,
    [ZipCode] [VARCHAR](5) NULL,
    [City] [VARCHAR](200) NULL,
    [STATE] [VARCHAR](50) NULL,
    [StateAbbreviation] [VARCHAR](2) NULL,
    [County] [VARCHAR](50) NULL,
    [Unused1] [VARCHAR](5) NULL,
    [Unused2] [VARCHAR](1) NULL,
    [Latitude] [DECIMAL](8,5) NULL,
    [Longitude] [DECIMAL](8,5) NULL,
    [Unused3] [VARCHAR](1) NULL
    )


DECLARE @bulk_cmd VARCHAR(1000)
SET @bulk_cmd = 'BULK INSERT ZipCodesTemp
   FROM ''C:YourFolderUS.txt''
   WITH (FIELDTERMINATOR=''t'', ROWTERMINATOR = '''+CHAR(10)+''')'
 
EXEC(@bulk_cmd)
```

Now you will create this table

```sql
CREATE TABLE ZipCodes(
    [Country] [VARCHAR](2)  NULL,
    [ZipCode] [VARCHAR](5) NOT NULL,
    [City] [VARCHAR](200)  NULL,
    [STATE] [VARCHAR](50)  NULL,
    [StateAbbreviation] [VARCHAR](2)  NULL,
    [County] [VARCHAR](50) NULL,
    [Latitude] [DECIMAL](8,5) NOT NULL,
    [Longitude] [DECIMAL](8,5) NOT NULL,
    [GeogCol1] [GEOGRAPHY]  NULL,
    [GeogColTemp] [varchar](100) NULL
    )
```

There is at least one duplicate row in this file so we will import only uniques

```sql
INSERT  ZipCodes (Country,ZipCode,City,STATE,StateAbbreviation,County,Latitude,Longitude)
SELECT DISTINCT Country,ZipCode,City,STATE,StateAbbreviation,County,Latitude,Longitude
FROM  ZipCodesTemp
```

Our next step will be to update the GeogCol1 table with something that SQL server can understand.
  
Here is some sample code that displays the format of this datatype

```sql
DECLARE @h geography;
SET @h = geography::STGeomFromText('POINT(-77.36750 38.98390)', 4326);
SELECT @h
```

output
  
———————————————-
  
0xE6100000010C6744696FF07D4340EC51B81E855753C0

As you can see it is some binary data. This data is using the World Geodetic System 1984 (WGS 84)

To see if this is supported in your database you can run this query

```sql
SELECT * FROM sys.spatial_reference_systems
```

And yes in my database it has a spatial\_reference\_id of 4326

```sql
SELECT * FROM sys.spatial_reference_systems
WHERE spatial_reference_id = 4326
```

Here is the meta data

GEOGCS["WGS 84", DATUM["World Geodetic System 1984",
  
ELLIPSOID["WGS 84", 6378137, 298.257223563]], PRIMEM["Greenwich", 0],
  
UNIT["Degree", 0.0174532925199433]]

Back to our code, in order to have this data in our table

SET @h = geography::STGeomFromText('POINT(-77.36750 38.98390)', 4326);

We need to do some things, first we update our temp column

```sql
UPDATE zipcodes 
SET GeogColTemp= 'POINT(' + convert(varchar(100),longitude) 
+' ' +  convert(varchar(100),latitude) +')'
```

Now we can update out geography column

```sql
UPDATE zipcodes 
SET GeogCol1 =  geography::STGeomFromText(GeogColTemp,4326)
```

We can drop the temp column now

```sql
ALTER TABLE zipcodes DROP COLUMN GeogColTemp
```

Now we have to add a primary key, this is needed because otherwise we won't be able to create our spatial index and the following message would be displayed

Server: Msg 12008, Level 16, State 1, Line 1
  
Table 'zipcodes' does not have a clustered primary key as required by the spatial index. Make sure that the primary key column exists on the table before creating a spatial index.

```sql
ALTER TABLE zipcodes ADD 
	CONSTRAINT [PK_ZipCode] PRIMARY KEY  CLUSTERED 
	(
		Zipcode,
		Longitude
	) WITH  FILLFACTOR = 100 
```

Create the spatial index

```sql
CREATE SPATIAL INDEX SIndx_SpatialTable_geography_col1 
   ON zipcodes(GeogCol1);
```

first I will show you an example to calculate the distance that you can execute

```sql
DECLARE @g geography;
DECLARE @h geography;
SET @h = geography::STGeomFromText('POINT(-77.36750 38.98390)', 4326);
SET @g = geography::STGeomFromText('POINT(-77.36160 38.85570)', 4326);
SELECT @g.STDistance(@h)/1609.344;
```

as you can see the distance is 8.8490611480890067

Now I want to see all the zipcode which are within 20 miles of zipcode 10028 (yes I used to live there)

Here is a way that will take a long time since it is not sargable, this will take about 2 seconds

```sql
SELECT h.* 
FROM zipcodes g 
JOIN zipcodes h on g.zipcode <> h.zipcode
AND g.zipcode = '10028'
AND h.zipcode <> '10028'
WHERE g.GeogCol1.STDistance(h.GeogCol1)/1609.344 <= 20
```

Now we all know functions on the left side of the operator are bad, here is how this is optimized, we switch the calculation to the right side of the = sign

```sql
SELECT h.* 
FROM zipcodes g 
JOIN zipcodes h on g.zipcode <> h.zipcode
AND g.zipcode = '10028'
AND h.zipcode <> '10028'
WHERE g.GeogCol1.STDistance(h.GeogCol1)<=(20 * 1609.344)
```

that ran in between 15 and 60 milliseconds

To find everything between 10 and 20 miles you can use this

```sql
SELECT h.* 
FROM zipcodes g 
JOIN zipcodes h on g.zipcode <> h.zipcode
AND g.zipcode = '10028'
AND h.zipcode <> '10028'
WHERE g.GeogCol1.STDistance(h.GeogCol1)<=(20 * 1609.344)
AND g.GeogCol1.STDistance(h.GeogCol1)>= (10 * 1609.344)
```

As you can see doing stuff like this on SQL Server 2008 is fairly easy because of the geograpy data type

\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][2] forum or our [Microsoft SQL Server Admin][3] forum**<ins></ins>

 [1]: /index.php/DataMgmt/DataDesign/sql-server-zipcode-latitude-longitude-pr
 [2]: http://forum.ltd.local/viewforum.php?f=17
 [3]: http://forum.ltd.local/viewforum.php?f=22