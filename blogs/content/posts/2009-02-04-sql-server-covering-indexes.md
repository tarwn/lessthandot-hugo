---
title: SQL Server covering indexes
author: George Mastros (gmmastros)
type: post
date: 2009-02-04T18:08:44+00:00
ID: 316
url: /index.php/datamgmt/datadesign/sql-server-covering-indexes/
views:
  - 19968
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Microsoft SQL Server

---
Most developers know that indexes can help speed up your queries, but what happens if you are getting nice index seeks and the performance is still not acceptable? In some cases, a covering index can improve your performance tremendously. This blog will help you identify what the covering index should be and how to test your query to make sure it is being used.

In SQL Server Management Studio, you can see the execution plan by pressing CTRL-M and then run your query. With Query Analyzer, use CTRL-K. After you run the query, you will see a new tab for the execution plan.

There is a 'type' of index that performs better than a clustered index. You can create a covering index. This is more of an idea (there is no syntax for COVERING in SQL). It sometimes helps to think of an index as a separate table. It's not, but for the moment, think of it that way. When you create an index, an object is created in the database that has all of the data in the index. SQL Server can then use this index to speed up your queries. Now, consider a query that only uses a couple of columns in your table. If you create an index that has all of the data from those columns, then there is no reason for the query to use the table (because all of the data is found in the index).

For example:

sql
Create Table PeopleTest(Id int Identity(1,1), Name VarChar(20), FavoriteColor VarChar(20))

Create Clustered Index idx_PeopleTest_Id On PeopleTest(id)


Insert Into PeopleTest(Name, FavoriteColor) Values('George', 'Orange')
Insert Into PeopleTest(Name, FavoriteColor) Values('John',   'Green')
Insert Into PeopleTest(Name, FavoriteColor) Values('Denis',  'Black')
```<pre style="color:green;">Id          Name                 FavoriteColor
----------- -------------------- --------------------
1           George               Orange
2           John                 Green
3           Denis                Black</pre>

Now, let's look at some queries.

sql
Select FavoriteColor
From   PeopleTest
Where  Id = 2
```
This will return 'Green'. Simple, right. Well… here's what's happening behind the scenes. SQL Server will use the clustered index to quickly locate the row where ID = 2. It will then go to the table to get FavoriteColor. Essentially, there are 2 steps. Locate the row in the index and use it to look up the row in the table.

Now, add this nonclustered index:

sql
Create nonclustered index idx_PeopleTest_Id_FavoriteColor On PeopleTest(Id, FavoriteColor)
```

Notice that there are 2 columns in the index (Id and FavoriteColor). Now, let's run the exact same query and look at the execution plan again.

sql
Select FavoriteColor
From   PeopleTest
Where  Id = 2
```
This time, you will see that there is an index seek (instead of clustered index seek). You see, the index has id and favorite color in it, so SQL Server will look up the row in the index and use the FavoriteColor data that it finds. There's no need to use any data from the actual table itself because all the data it needs is in the index.

Now, let's look at another query.

sql
Select Name, FavoriteColor
From   PeopleTest
Where  Id = 2
```
This time, when you run the query, it goes back to a clustered index seek (because that index has less columns and will therefore perform better). There are 3 columns that this query uses. Name, FavoriteColor, and ID. Since there are no indexes that contain all of these column, the best execution plan is to look up the row based on the where clause and then go to the table data to get the Name and FavoriteColor.

Ok, so you're probably thinking, I can create an index that has all 3 columns to improve performance, right? The answer is, yes, but you need to be careful about it too. If you create this index…

sql
Create nonclustered index idx_PeopleTest_Name_Id_FavoriteColor On PeopleTest(Name, Id, FavoriteColor)
```

The index will not be used because the first column in the index is name. This index would need to be scanned (not seeked) to get the row (and the data).

Think of the index data sorted first by Name, then ID, and finally FavoriteColor, like this…

<pre style="color:green;">Name                 Id          FavoriteColor
-------------------- ----------- --------------------
Denis                3           Black
George               1           Orange
John                 2           Green</pre>

Since the where clause is looking for Id = 2, it would need to examine every row in the index to find it. For this query, the covering index would look like this:

sql
Create nonclustered index idx_PeopleTest_Id_Name_FavoriteColor On PeopleTest(Id, Name, FavoriteColor)
```

Now, when you run the query, you will see an index seek again.

You need to be careful with indexes though. Having too many indexes on a table will slow down your performance because every insert, update, and delete statement will affect the data in the index too.