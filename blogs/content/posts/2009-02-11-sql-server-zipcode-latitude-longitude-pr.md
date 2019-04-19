---
title: SQL Server Zipcode Latitude/Longitude proximity distance search
author: George Mastros (gmmastros)
type: post
date: 2009-02-11T17:40:00+00:00
ID: 323
excerpt: 'Have you ever wanted to add functionality that allows you to perform simple radius searches based on Zip Code?  For example, Show me all the cities within 20 miles of a certain zip code or show me the 5 closest retail locations. You can easily add this&hellip;'
url: /index.php/datamgmt/datadesign/sql-server-zipcode-latitude-longitude-pr/
views:
  - 150833
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Microsoft SQL Server

---
Have you ever wanted to add functionality that allows you to perform simple radius searches based on Zip Code? For example, Show me all the cities within 20 miles of a certain zip code or show me the 5 closest retail locations. You can easily add this functionality to your database by importing a zip code database and writing a little code. This article will show you where to get zip code data, how to import it to your database, and how to use the data to perform proximity searches.

Denis and I decided to show you how you can do simple radius searches based on Zip Codes, he did the SQL 2008 version here: /index.php/DataMgmt/DataDesign/sql-server-2008-proximity-search-with-th (using the geography data type) and I did the SQL Server 2000/2005 version.

The first thing we need to do is load our data. There are various sources for this data, ranging from free to expensive monthly subscriptions. One source of free data is: http://download.geonames.org/export/zip/

<span class="MT_red"><strong>** Note:</strong></span> Based on a comment from Steve_O, the format of the geonames file recently changed. I have no control over those changes, and am not notified when changes occur. If you have difficulty loading the data from the file in to the table, you may need to investigate the structure of the data to see if anything has changed.

Once you have downloaded your data, the next step is to import it in to your database. You can use the following script to do it.

sql
If Exists(Select * 
          From   Information_Schema.Tables 
          Where  Table_Name = 'ZipCodes' 
                 And Table_Type = 'Base Table')
  Drop Table ZipCodes

CREATE TABLE [dbo].[ZipCodes](
[country code] [varchar](2) NULL,
[postal code] [varchar](20) NULL,
[place name] [varchar](180) NULL,
[admin name1] [varchar](100) NULL,
[admin code1] [varchar](20) NULL,
[admin name2] [varchar](100) NULL,
[admin code2] [varchar](20) NULL,
[admin name3] [varchar](100) NULL,
[admin code3] [varchar](20) NULL,
[latitude] [decimal](8, 4) NULL,
[longitude] [decimal](8, 4) NULL,
[accuracy] [int] NULL
)

DECLARE @bulk_cmd varchar(1000)
SET @bulk_cmd = 'BULK INSERT ZipCodes
    FROM ''C:YourFolderUS.txt''
    WITH (FIELDTERMINATOR=''t'', ROWTERMINATOR = '''+CHAR(10)+''')'

EXEC(@bulk_cmd)

Alter Table ZipCodes Drop Column Unused1, Unused2, Unused3

CREATE CLUSTERED INDEX IX_ZipCodes_Zip ON dbo.ZipCodes(ZipCode, Longitude, Latitude)
GO
CREATE NONCLUSTERED INDEX IX_ZipCodes_Longitude_Latitude ON dbo.ZipCodes(Longitude,Latitude, ZipCode)

GO

Select * From ZipCodes
```
Next, we'll need to add a couple of functions that will allow us to use this data.

The original function that calculates distances has been replaced with this one. There was a flaw in the original version. Based on rounding errors, the calculations fed in to the arc-cosine (acos) function could be greater than 1 or less than -1 which would cause a domain error. To accommodate this flaw, I perform the calculations in multiple steps so that I can catch the values that result in the domain error. 

Credit for finding the flaw goes to <span class="MT_red"><strong>Chris</strong></span>. Thank you.

sql
CREATE Function [dbo].[CalculateDistance]
	(@Longitude1 Decimal(8,5), 
	@Latitude1   Decimal(8,5),
	@Longitude2  Decimal(8,5),
	@Latitude2   Decimal(8,5))
Returns Float
As
Begin
Declare @Temp Float

Set @Temp = sin(@Latitude1/57.2957795130823) * sin(@Latitude2/57.2957795130823) + cos(@Latitude1/57.2957795130823) * cos(@Latitude2/57.2957795130823) * cos(@Longitude2/57.2957795130823 - @Longitude1/57.2957795130823)

if @Temp > 1 
	Set @Temp = 1
Else If @Temp < -1
	Set @Temp = -1

Return (3958.75586574 * acos(@Temp)	) 

End
```
sql
Create Function [dbo].[LatitudePlusDistance](@StartLatitude Float, @Distance Float) Returns Float
As
Begin
    Return (Select @StartLatitude + Sqrt(@Distance * @Distance / 4766.8999155991))
End
```
sql
Create Function [dbo].[LongitudePlusDistance]
    (@StartLongitude Float,
    @StartLatitude Float,
    @Distance Float)
Returns Float
AS
Begin
    Return (Select @StartLongitude + Sqrt(@Distance * @Distance / (4784.39411916406 * Cos(2 * @StartLatitude / 114.591559026165) * Cos(2 * @StartLatitude / 114.591559026165))))
End
```
Finally, we can begin writing some interesting queries. For example, return a list of zipcodes within 20 miles of zipcode 20013 (Washington, DC).

sql
-- Declare some variables that we will need.
Declare @Longitude Decimal(8,5),
        @Latitude Decimal(8,5),
        @MinLongitude Decimal(8,5),
        @MaxLongitude Decimal(8,5),
        @MinLatitude Decimal(8,5),
        @MaxLatitude Decimal(8,5)

-- Get the lat/long for the given zip
Select @Longitude = Longitude,
       @Latitude = Latitude
From   ZipCodes
Where  ZipCode = '20013'

-- Calculate the Max Lat/Long
Select @MaxLongitude = dbo.LongitudePlusDistance(@Longitude, @Latitude, 20),
       @MaxLatitude = dbo.LatitudePlusDistance(@Latitude, 20)

-- Calculate the min lat/long
Select @MinLatitude = 2 * @Latitude - @MaxLatitude,
       @MinLongitude = 2 * @Longitude - @MaxLongitude

-- The query to return all zips within a certain distance
Select ZipCode
From   ZipCodes
Where  Longitude Between @MinLongitude And @MaxLongitude
       And Latitude Between @MinLatitude And @MaxLatitude
       And dbo.CalculateDistance(@Longitude, @Latitude, Longitude, Latitude) <= 20
```
You could also use this for a “store locator”. On a website, potential customers could enter their zipcode and you could present a list of top 5 closest stores.
  
The query shown below assumes you have a Stores table with a ZipCode column.

sql
Declare @Longitude Decimal(8,5)
Declare @Latitude Decimal(8,5)

Select	@Longitude = Longitude,
		@Latitude = Latitude
From	ZipCodes
Where	ZipCode = '20013'

Select  Top 5 Stores.StoreName, ZipCodes.City,  dbo.CalculateDistance(@Longitude, @Latitude, ZipCodes.Longitude, ZipCodes.Latitude) As Distance 
From	@Stores As Stores
		Inner Join ZipCodes 
			On Stores.ZipCode = ZipCodes.ZipCode
Order By dbo.CalculateDistance(@Longitude, @Latitude, ZipCodes.Longitude, ZipCodes.Latitude)
```
As you can see, these queries perform very well, and are relatively easy to write.