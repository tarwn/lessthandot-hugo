---
title: Including an Aggregated Column’s Related Values – Part 2
author: Naomi Nosonovsky
type: post
date: 2009-10-27T05:51:34+00:00
ID: 545
url: /index.php/datamgmt/datadesign/including-an-aggregated-column-s-related-2/
views:
  - 14592
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
This blog post contains test cases for the first article in the series [Including an Aggregated Column's Related Values][1]

There is also an excellent (as always) article by Itzik Ben Gan [Optimizing TOP N per Group Queries][2]. I suggest to read and try the recommendations from that article first.

There is a related [thread][3] on MSDN forum where different methods are also compared in speed. 

One more [related discussion on MSDN][4] with more performance tests.

Take a look also at this [T-SQL challenge][5] 

sql
USE AdventureWorks
 
-- This solution is SQL Server 2000 compatible - correlated subquery
SET STATISTICS TIME ON
SELECT Cust.[CustomerID],
      Cust.[TerritoryID],
      Cust.[AccountNumber],
      Cust.[CustomerType], 
      Ord.[SalesOrderID],      
      Ord.[OrderDate],
      Ord.[DueDate],
      Ord.[ShipDate],
      Ord.[Status],      
      Ord.[SubTotal], Ord.[TaxAmt],
      Ord.[Freight],  Ord.[TotalDue]  FROM Sales.Customer Cust 
INNER JOIN Sales.SalesOrderHeader Ord 
ON Cust.CustomerID = Ord.CustomerID 
WHERE Ord.OrderDate = 
(SELECT MAX(OrderDate) AS LastDate FROM  
Sales.SalesOrderHeader OH WHERE OH.CustomerID = Ord.CustomerID) 
 
-- The above solution will return duplicate records if there is more than 1 maximum date for a given customer
 
SELECT Cust.[CustomerID],
      Cust.[TerritoryID],
      Cust.[AccountNumber],
      Cust.[CustomerType], 
      Ord.[SalesOrderID],      
      Ord.[OrderDate],
      Ord.[DueDate],
      Ord.[ShipDate],
      Ord.[Status],      
      Ord.[SubTotal], Ord.[TaxAmt],
      Ord.[Freight],  Ord.[TotalDue]  FROM Sales.Customer Cust 
INNER JOIN Sales.SalesOrderHeader Ord 
ON Cust.CustomerID = Ord.CustomerID 
WHERE Ord.SalesOrderID = 
(SELECT top 1 SalesOrderID FROM  
Sales.SalesOrderHeader OH WHERE OH.CustomerID = Cust.CustomerID order by OrderDate DESC)  


-- This is SQL Server 2000 compatible solution based on derived table idea
SELECT Cust.[CustomerID],
      Cust.[TerritoryID],
      Cust.[AccountNumber],
      Cust.[CustomerType], 
      Ord.[SalesOrderID],      
      Ord.[OrderDate],
      Ord.[DueDate],
      Ord.[ShipDate],
      Ord.[Status],      
      Ord.[SubTotal], Ord.[TaxAmt],
      Ord.[Freight],  Ord.[TotalDue] FROM Sales.Customer Cust 
INNER JOIN Sales.SalesOrderHeader Ord 
ON Cust.CustomerID = Ord.CustomerID 
INNER join (SELECT CustomerID, MAX(OrderDate) AS LastDate FROM  
Sales.SalesOrderHeader OH GROUP BY CustomerID) LastOrder 
ON Cust.CustomerID = LastOrder.CustomerID and Ord.OrderDate = LastOrder.LastDate 
 
-- The same comment as above applies - it will return duplicate records in case of several same last dates for the Customer
 
 
--- Two solutions bellow are only available in SQL Server 2005 and up - if we want them to return multiple records
--- We would need to use RANK() function instead of ROW_NUMBER()
 
SELECT * FROM (SELECT Cust.[CustomerID],
      Cust.[TerritoryID],
      Cust.[AccountNumber],
      Cust.[CustomerType], 
      Ord.[SalesOrderID],      
      Ord.[OrderDate],
      Ord.[DueDate],
      Ord.[ShipDate],
      Ord.[Status],      
      Ord.[SubTotal], Ord.[TaxAmt],
      Ord.[Freight],  Ord.[TotalDue], 
      ROW_NUMBER() OVER (PARTITION BY Ord.CustomerID 
      ORDER BY OrderDate DESC) AS rown  FROM Sales.Customer Cust 
INNER JOIN Sales.SalesOrderHeader Ord 
ON Cust.CustomerID = Ord.CustomerID) Ordered WHERE rown = 1
 
SELECT TOP 1 WITH ties  Cust.[CustomerID],
      Cust.[TerritoryID],
      Cust.[AccountNumber],
      Cust.[CustomerType], 
      Ord.[SalesOrderID],      
      Ord.[OrderDate],
      Ord.[DueDate],
      Ord.[ShipDate],
      Ord.[Status],      
      Ord.[SubTotal], Ord.[TaxAmt],
      Ord.[Freight],  Ord.[TotalDue] FROM Sales.Customer Cust 
INNER JOIN Sales.SalesOrderHeader Ord 
ON Cust.CustomerID = Ord.CustomerID 
ORDER BY ROW_NUMBER() OVER (PARTITION BY Ord.CustomerID 
      ORDER BY OrderDate DESC)
 
 
-- Compound key solution that outperforms all other solutions
SELECT Cust.[CustomerID],
      Cust.[TerritoryID],
      Cust.[AccountNumber],
      Cust.[CustomerType], 
      Ord.[SalesOrderID],      
      Ord.[OrderDate],
      Ord.[DueDate],
      Ord.[ShipDate],
      Ord.[Status],      
      Ord.[SubTotal], Ord.[TaxAmt],
      Ord.[Freight],  Ord.[TotalDue] FROM Sales.Customer Cust 
INNER JOIN Sales.SalesOrderHeader Ord 
ON Cust.CustomerID = Ord.CustomerID 
INNER join (SELECT CustomerID, 
MAX(CONVERT(NVARCHAR(30), OrderDate, 126) + CAST(SalesOrderID AS CHAR(12))) AS CompoundKey FROM  
Sales.SalesOrderHeader OH GROUP BY CustomerID) LastOrder 
ON Cust.CustomerID = LastOrder.CustomerID and Ord.SalesOrderID  = CAST(RIGHT(LastOrder.CompoundKey,12) AS INT)
SET STATISTICS TIME OFF
```

With the results
  
<code class="codespan"></p>
<p>(19127 row(s) affected)</p>
<p>(1 row(s) affected)</p>
<p> SQL Server Execution Times:<br />
   CPU time = 327 ms,  elapsed time = 825 ms.</p>
<p>(19119 row(s) affected)</p>
<p>(1 row(s) affected)</p>
<p> SQL Server Execution Times:<br />
   CPU time = 578 ms,  elapsed time = 1261 ms.</p>
<p>(19127 row(s) affected)</p>
<p>(1 row(s) affected)</p>
<p> SQL Server Execution Times:<br />
   CPU time = 265 ms,  elapsed time = 810 ms.</p>
<p>(19119 row(s) affected)</p>
<p>(1 row(s) affected)</p>
<p> SQL Server Execution Times:<br />
   CPU time = 359 ms,  elapsed time = 865 ms.</p>
<p>(19119 row(s) affected)</p>
<p>(1 row(s) affected)</p>
<p> SQL Server Execution Times:<br />
   CPU time = 436 ms,  elapsed time = 972 ms.</p>
<p>(19119 row(s) affected)</p>
<p>(1 row(s) affected)</p>
<p> SQL Server Execution Times:<br />
   CPU time = 375 ms,  elapsed time = 778 ms.<br />
</code>

The execution plans 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/ExecutionPlans.gif" alt="" title="" width="1862" height="1902" />
</div>

This illustrates a real case scenario &#8211; see [ASP.NET forum's thread][6]

sql
CREATE TABLE Items(
	ItemId int NOT NULL,
	ItemName varchar(20) NOT NULL,
	ItemDesciption varchar(20) NULL,
	Supplier varchar(20) NULL,
	ItemValue numeric(10, 2) NULL,
 CONSTRAINT PK_Items      PRIMARY KEY CLUSTERED (ItemId ASC),
 CONSTRAINT UQ_Items_Name UNIQUE   NONCLUSTERED (ItemName ASC)
)

GO

CREATE TABLE Bid(
	BidId int IDENTITY(1,1) NOT NULL,
	ItemId int NOT NULL,
	BidAmount decimal(10, 2) NOT NULL,
	BidDateTime datetime NOT NULL,
 CONSTRAINT PK_Bid PRIMARY KEY CLUSTERED (BidId ASC) 
)

GO

CREATE NONCLUSTERED INDEX IX_Bid 
    ON dbo.Bid  (ItemId ASC, BidAmount DESC)

ALTER TABLE dbo.Bid  
  ADD CONSTRAINT FK_Bid_Items1 
  FOREIGN KEY(ItemId)
  REFERENCES dbo.Items (ItemId)
GO

ALTER TABLE dbo.Bid CHECK CONSTRAINT FK_Bid_Items1
GO
```
sql
SET NOCOUNT ON
Declare @i int
Declare @j int
Declare @r int

Set @i=2000
While @i<=100000 Begin
   Insert Into Items( ItemId, ItemName, ItemDesciption, ItemValue )
   Select @i,
          'Item' + Right('0000'+ Cast(@i as varchar(5)),5),
          'Desc' + Right('0000'+ Cast(@i as varchar(5)),5),
          cast(Rand()*1000. as numeric(10,2))
   Select @r = Rand()*10, @j=1
   While @j<=@r Begin
       Insert Into Bid(ItemId, BidAmount, BidDateTime)
       Select @i, 
              cast(Rand()*1000. as numeric(10,2)),
              GetDate()
      Set @j=@j+1
   end
              
   Set @i=@i+1
end
GO
```
### 6 cases combined:

sql
set nocount on
declare @ItemsCount int, @BidsCount int

select @ItemsCount = COUNT(*) from Items
select @BidsCount = COUNT(*) from Bid

print 'Test case - Number of Items - ' + cast(@ItemsCount as varchar(10)) + ' Bids count - ' + cast(@BidsCount as varchar(10))


print replicate('-',50) + char(13) + 'Nikola''s solution - TOP clause ' 
set statistics time on
SELECT Bid.BidId, 
       Bid.ItemId, 
       Bid.BidAmount, 
       Bid.BidDateTime, 
       Items.ItemId AS Expr1, 
       Items.ItemName, 
       Items.ItemDesciption, 
       Items.Supplier,
       Items.ItemValue
  From Items
  Left Join Bid 
         on Bid.ItemId = Items.ItemId 
        and Bid.BidId in (Select Top 1 BidId
                            From Bid b
                           Where b.ItemId=Items.ItemId
                           Order By b.BidAmount desc) order by Items.ItemId


set statistics time off
print replicate('-',50) + char(13) + 'Derived table solution - can have duplicates '
set statistics time on
SELECT X.BidId, 
       X.ItemId, 
       X.BidAmount, 
       X.BidDateTime, 
       Items.ItemId AS Expr1, 
       Items.ItemName, 
       Items.ItemDesciption, 
       Items.Supplier,
       Items.ItemValue
  From Items
  Left Join (select Bid.* from Bid inner join ( 
         Select ItemId, MAX(BidAmount) as BidAmount from Bid group by ItemId ) B on
                           b.ItemId=Bid.ItemId and Bid.BidAmount = B.BidAmount) X on Items.ItemId = X.ItemId order by Items.ItemId


set statistics time off                           
print replicate('-',50) + char(13) + 'Correlated subquery solution ' 
set statistics time on
SELECT Bid.BidId, 
       Bid.ItemId, 
       Bid.BidAmount, 
       Bid.BidDateTime, 
       Items.ItemId AS Expr1, 
       Items.ItemName, 
       Items.ItemDesciption, 
       Items.Supplier,
       Items.ItemValue
  From Items
  Left Join Bid on Bid.ItemId = Items.ItemId where Bid.ItemId IS NULL OR 
  Bid.BidAmount = (select MAX(BidAmount) from Bid b where b.ItemId = Items.ItemId) order by Items.ItemId  
  
set statistics time off
print replicate('-',50) + char(13) + 'ROW_NUMBER() solution '
set statistics time on
select * from (SELECT Bid.BidId, 
       Bid.ItemId, 
       Bid.BidAmount, 
       Bid.BidDateTime, 
       Items.ItemId AS Expr1, 
       Items.ItemName, 
       Items.ItemDesciption, 
       Items.Supplier,
       Items.ItemValue, ROW_NUMBER() over (PARTITION by Items.ItemID order by Bid.BidAmount Desc) as rn 
  From Items
  Left Join Bid on Bid.ItemId = Items.ItemId) X where rn = 1 order by X.ItemId                          

set statistics time off
print replicate('-',50) + char(13) + 'Row_number() - slight variation '
set statistics time on
SELECT top 1 with ties Bid.BidId, 
       Bid.ItemId, 
       Bid.BidAmount, 
       Bid.BidDateTime, 
       Items.ItemId AS Expr1, 
       Items.ItemName, 
       Items.ItemDesciption, 
       Items.Supplier,
       Items.ItemValue  
  From Items
  Left Join Bid on Bid.ItemId = Items.ItemId 
  order by ROW_NUMBER() over (PARTITION by Items.ItemID order by Bid.BidAmount Desc) 

set statistics time off
  print replicate('-',50) + char(13) + 'Finally - compound key solution ' 
  set statistics time on
  select Bid.ItemId, 
       Bid.BidAmount, 
       cast(right(Bid.CompKey,30) as datetime) as BidDateTime, 
       Items.ItemId AS Expr1, 
       Items.ItemName, 
       Items.ItemDesciption, 
       Items.Supplier,
       Items.ItemValue  
  From Items
  Left Join (select ItemID, Max(BidAmount) as BidAmount, 
  MAX(cast(BidAmount as char(10)) + convert(char(30),BidDateTime,126)) as CompKey from Bid group by ItemId) Bid
  on Items.ItemId = Bid.ItemId order by Items.ItemId
  
  set statistics time off
    
```

### Test results in SQL Server 2008 Express

<code class="codespan"><br />
Test case - Number of Items - 100000 Bids count - 497381<br />
--------------------------------------------------<br />
Nikola's solution - TOP clause </p>
<p> SQL Server Execution Times:<br />
   CPU time = 842 ms,  elapsed time = 1904 ms.<br />
--------------------------------------------------<br />
Derived table solution - can have duplicates </p>
<p> SQL Server Execution Times:<br />
   CPU time = 624 ms,  elapsed time = 2734 ms.<br />
--------------------------------------------------<br />
Correlated subquery solution </p>
<p> SQL Server Execution Times:<br />
   CPU time = 3729 ms,  elapsed time = 6418 ms.<br />
--------------------------------------------------<br />
ROW_NUMBER() solution </p>
<p> SQL Server Execution Times:<br />
   CPU time = 1342 ms,  elapsed time = 4175 ms.<br />
--------------------------------------------------<br />
Row_number() - slight variation </p>
<p> SQL Server Execution Times:<br />
   CPU time = 2137 ms,  elapsed time = 4633 ms.<br />
--------------------------------------------------<br />
Finally - compound key solution </p>
<p> SQL Server Execution Times:<br />
   CPU time = 1108 ms,  elapsed time = 2742 ms.</p>
<p></code>

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][7] forum or our [Microsoft SQL Server Admin][8] forum**<ins></ins>

 [1]: /index.php/DataMgmt/DBProgramming/MSSQLServer/including-an-aggregated-column-s-related
 [2]: http://www.windowsitpro.com/article/departments/Optimizing-TOP-N-Per-Group-Queries.aspx
 [3]: http://social.msdn.microsoft.com/Forums/en-US/transactsql/thread/824546a0-0c03-4af6-86c6-8df9f5381f2b
 [4]: http://social.msdn.microsoft.com/Forums/en-US/transactsql/thread/0217ddf4-bcab-4f2b-a81c-70841753cb23
 [5]: http://www.sqlmag.com/article/articleid/102883/102883.html
 [6]: http://forums.asp.net/p/1462968/3371370.aspx
 [7]: http://forum.ltd.local/viewforum.php?f=17
 [8]: http://forum.ltd.local/viewforum.php?f=22