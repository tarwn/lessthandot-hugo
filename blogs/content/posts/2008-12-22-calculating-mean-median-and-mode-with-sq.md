---
title: Calculating Mean, Median and Mode with SQL Server
author: George Mastros (gmmastros)
type: post
date: 2008-12-22T14:57:57+00:00
ID: 258
excerpt: |
  Calculating Median and Mode with SQL Server can be frustrating for some developers, but it doesn't have to be.  Often times, inexperienced developers will attempt to write this with procedural programming practices, but set based methods do exist.
  
  Be&hellip;
url: /index.php/datamgmt/datadesign/calculating-mean-median-and-mode-with-sq/
views:
  - 219796
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Microsoft SQL Server

---
Calculating Median and Mode with SQL Server can be frustrating for some developers, but it doesn't have to be. Often times, inexperienced developers will attempt to write this with procedural programming practices, but set based methods do exist.

Before showing you methods to calculate these values, it’s probably best to explain what they are.
  
Mean is another name for average. SQL Server has a built-in function to calculate this value.

<pre>Data
----
1
2
5
5
5
6
6
6
7
9
10
</pre>

To calculate the average, sum the data and divide by the number of rows. In this case, 1 + 2 + 5 + 5 + 5 + 6 + 6 + 6 + 7 + 9 + 10 = 62. 62/11 = 5.636363

Median represents the &#8216;middle' value. To calculate the median by hand, you sort the values and return the value that appears in the middle of the list. If there is an odd number of items, there will be exactly one value in the middle of the list. If there is an even number of items, you average the 2 values that are closest to the middle.

<pre>Data
1
2
5
5
5
<span style="color:red;">6</span>
6
6
7
9
10
</pre>

Since there is an odd number of rows, the row appearing in the middle of the list contains your median value.

<pre>Data
1
2
5
5
<span style="color:red;">5
6</span>
6
6
7
9
</pre>

Now, there is an even number of rows. The median for this data set is (5 + 6)/2 = 5.5. Simply take the average of the 2 values appearing in the middle of the data set.

**MODE**
  
The mode for a data set is the item(s) that appear most frequently. To calculate this by hand, you write a distinct list of values and count the number of times a value appears. The value the appears the most is your mode.

<pre>Data	Frequency
----    ---------
1	1
2	1
<span style="color:red;">5	3
6	3</span>
7	1
9	1
10	1
</pre>

This data set is considered to be Bi-Modal because there are 2 values with the same frequency. With this data set, the modes are 5 and 6.

For demonstration purposes, I will create a table variable and populate it with data. All code will be based on this table variable. The data type for the data column will be Decimal(10,5). If we used an integer column, then we would need to concern ourselves with integer math issues, which is not the focus of this blog.

**AVERAGE**
  
As I stated earlier, SQL Server has a built-in function for calculating the average. The Avg function will ignore rows with NULL. So the average of 1, 2, NULL is 1.5 because the sum of the data is 3 and there are 2 rows that are not NULL. 3/2 = 1.5.

sql
Declare @Temp Table(Id Int Identity(1,1), Data Decimal(10,5))

Insert into @Temp Values(1)
Insert into @Temp Values(2)
Insert into @Temp Values(5)
Insert into @Temp Values(5)
Insert into @Temp Values(5)
Insert into @Temp Values(6)
Insert into @Temp Values(6)
Insert into @Temp Values(6)
Insert into @Temp Values(7)
Insert into @Temp Values(9)
Insert into @Temp Values(10)
Insert into @Temp Values(NULL)

Select Avg(Data)
From   @Temp

-- 5.636363
```

**MEDIAN**
  
To calculate the median, we will select the last value in the top 50 percent of rows, and the first value in the bottom 50 percent (all while ignoring NULL values).
  
To get the last value in the top 50 percent of rows….

<pre>Select Top 1 Data
		From   (
				Select Top 50 Percent Data
				From	@Temp
				Where	Data Is NOT NULL
				Order By Data
				) As A
		Order By Data DESC</pre>

To get the first value in the last 50 percent of rows…

<pre>Select Top 1 Data
		From   (
				Select Top 50 Percent Data
				From	@Temp
				Where	Data Is NOT NULL
				Order By Data DESC
				) As A
		Order By Data Asc</pre>

Putting it all together:

sql
Declare @Temp Table(Id Int Identity(1,1), Data Decimal(10,5))

Insert into @Temp Values(1)
Insert into @Temp Values(2)
Insert into @Temp Values(5)
Insert into @Temp Values(5)
Insert into @Temp Values(5)
Insert into @Temp Values(6)
Insert into @Temp Values(6)
Insert into @Temp Values(6)
Insert into @Temp Values(7)
Insert into @Temp Values(9)
Insert into @Temp Values(10)
Insert into @Temp Values(NULL)

Select ((
		Select Top 1 Data
		From   (
				Select	Top 50 Percent Data
				From	@Temp
				Where	Data Is NOT NULL
				Order By Data
				) As A
		Order By Data DESC) + 
		(
		Select Top 1 Data
		From   (
				Select	Top 50 Percent Data
				From	@Temp
				Where	Data Is NOT NULL
				Order By Data DESC
				) As A
		Order By Data Asc)) / 2
-- 6
```
**MODE**
  
To Calculate the mode with sql server, we first need to get the counts for each value in the set. Then, we need to filter the data so that values equal to the count are returned.

sql
Declare @Temp Table(Id Int Identity(1,1), Data Decimal(10,5))

Insert into @Temp Values(1)
Insert into @Temp Values(2)
Insert into @Temp Values(5)
Insert into @Temp Values(5)
Insert into @Temp Values(5)
Insert into @Temp Values(6)
Insert into @Temp Values(6)
Insert into @Temp Values(6)
Insert into @Temp Values(7)
Insert into @Temp Values(9)
Insert into @Temp Values(10)
Insert into @Temp Values(NULL)

SELECT TOP 1 with ties DATA
FROM   @Temp
WHERE  DATA IS Not NULL
GROUP  BY DATA
ORDER  BY COUNT(*) DESC

```
As you can see, there are set based methods for calculating all of these values, which can be many times faster than calculating these values in a cursor.