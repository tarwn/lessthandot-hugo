---
title: Dynamic column names and fields in SSRS (Custom Matrix)
author: Ted Krueger (onpnt)
type: post
date: 2009-07-01T16:26:57+00:00
ID: 492
excerpt: I had no choice but to do work with creating dynamic column headings and 
  dynamically determine what field in my dataset should go where in a report today.
  Since this is the second time I've gone through this exercise and knowing the lack 
  of information
url: /index.php/datamgmt/datadesign/dynamic-column-names-and-fields-in-ssrs/
views:
  - 70469
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
I had no choice but to do work with creating dynamic column headings and dynamically determine what field in my dataset should go where in a report today. Since this is the second time I've gone through this exercise and knowing the lack of information out there on really how to do it, I thought it deserves a blog entry.

So here is the basis of the requirements. You have a query that uses PIVOT but thrown into the mix is the need for dynamic columns in the PIVOT. This is usually a task when you are going after things like current week plus the last 52 weeks. That was the case in this situation. I needed to bring in a dynamic set of columns to be used in PIVOT. The matrix in 2005 did not give me what I needed in the end result so this is the path I took.

First task is to write the procedure to use PIVOT with dynamic column headers. I'm not going to go into that method since it's well documented out there and out of scope. I will point you to [Pivots with Dynamic Columns in SQL Server 2005][1] as it explains the way to accomplish this well. 

I wrote something in AdventureWorks to for this example so if you have AdventureWorks floating around you should be able to read this and run through step for step with success.

Here is our procedure. I'm sure my methods will take great notice from my local TSQL friends 🙂 The dynamic SQL more so than anything...

```sql
Create Procedure GetSalesPerWeek
As
Declare @weeks_ordered Table (num varchar(3))
Declare @weeks Table (wk int)
Declare @date datetime
Declare @cols NVARCHAR(3000)
Declare @int int
Declare @col_pv varchar(2000)
Declare @query varchar(3000)


Set @int = 1
Set @date = getdate()

While @int <= 52
Begin
	Insert Into @weeks Values (@int)
	Set @int = @int + 1
End


Insert Into @weeks_ordered
Select 
wk
from @weeks
Order By
case When DatePart(wk,@date) - wk < 0
then DatePart(wk,@date) - wk + 53
Else DatePart(wk,@date) - wk
End Desc

Select @col_pv = STUFF(( SELECT  
                                '],[' + w.num
                        FROM  @weeks_ordered AS w
                        FOR XML PATH('')
                      ), 1, 2, '') + ']'

SELECT  @cols = STUFF(( SELECT  
                                '],0) as W' + Case When Cast(w.num - 1 as varchar(2)) = 0 Then '52' Else 
														Cast(w.num - 1 as varchar(2)) End + ',isnull([' + w.num
                        FROM  @weeks_ordered AS w
                        FOR XML PATH('')
                      ), 1, 2, '') + '],0) as W' + Cast(DatePart(wk,getdate()) as varchar(2))

 
if object_id('tempdb.dbo.#detail') is not null
		drop table #detail

create table #detail
(
AccountNumber varchar(10)
,PruductNumber varchar(25)
,OrderQty Int
,WeekNumber smallint
)


Insert into #detail
select 
	cust.AccountNumber
	,items.ProductNumber
	,det.OrderQty
	,DatePart(wk,hdr.ShipDate) WeekNumber
from 
Sales.SalesOrderHeader hdr
Inner Join Sales.SalesOrderDetail det on hdr.SalesOrderID = det.SalesOrderID
Inner Join Production.Product items on det.ProductID = items.ProductID
Inner Join Sales.Customer cust on hdr.CustomerID = cust.CustomerID
Inner Join @weeks ord on DatePart(wk,ShipDate) = wk
Where ShipDate >= dateadd(wk,-52,'2004-06-01')
Group By
	cust.AccountNumber
	,items.ProductNumber
	,det.OrderQty
	,DatePart(wk,hdr.ShipDate)
	,wk
Order By
case When DatePart(wk,ShipDate) - wk < 0
then DatePart(wk,ShipDate) - wk + 53
Else DatePart(wk,ShipDate) - wk
End


Set @query = 
'
Select	
	AccountNumber
	,PruductNumber
	, ' 	+ Right(@cols,Len(@cols)-10) + '
From
	#detail as sales
PIVOT (sum(OrderQty) FOR WeekNumber IN (' + @col_pv + ')) as pv
Order By AccountNumber
'
Exec(@query)

```
The results shows us the PIVOT results of each account number and the sales for the week of the year

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//pivot_1.gif" alt="" title="" width="628" height="354" />
</div>

The problem with all of this is the dynamic nature of the column names. In reporting services we're used to handling column names as static entities. So here is how we'll build our report given the fact these column names can and will change over time.

So create a new report in your solution and add a new DataSet. Make it a text call with the following statement

```sql
Exec GetSalesPerWeek
```

Run the DataSet to verify everything comes in ok.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//pivot_2.gif" alt="" title="" width="628" height="354" />
</div>

Now add a new table to your empty report. Add the account number and product number as you would normally. Next we need to figure out what week is actually first. To do this we're going to write a function in the code section of SSRS

In the layout tab go to Report and select Report Properties. This will give you the properties for the entire report. Select the Code tab. Copy and paste the following code into the window

```VB
Public Function GetColumnHeading(ByVal x As Integer)
       Dim WeeksArr As New System.Collections.ArrayList()
        Dim i As Long
        Dim CurrentWeek As Long

        CurrentWeek = DatePart(DateInterval.WeekOfYear, System.DateTime.Now)

        For i = 1 To 52
            WeeksArr.Add(1 + (i + CurrentWeek - 1) Mod 52)
        Next
        Return WeeksArr(x)
    End Function
```
This code was written by our own gmmastros. Thanks to him for this and the help it gave me when I needed it. Gets the job done and it does it quickly. 

Final results should look like this

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//pivot_3.gif" alt="" title="" width="628" height="367" />
</div>

Hit OK to save.

Now in the field next to the Product Number go ahead and enter an expression for the heading like this

=”W” & Code.GetColumnHeading(0)

Recall in our procedure we return each week as Wnn for the week number. So in our code we created an ArrayList filled up with the order we want. The same order we based the procedure off of. Now by using the index of the ArrayList we can simply call for the heading that should be all the way to the left (-51 weeks from the current) by means of index of 0. In the details textbox we can then simply do the following as well given the same guidelines

=Fields(“W” & Code.GetColumnHeading(0)).Value

Most developers don't know they can reference the fields by name in this manner. Usually it just isn't required and that is the case. It can be useful to note that you can dynamically fill the object name in though and get the same results as Fields!name.Value

I went ahead and put a few more columns and increased the index requested from the ArrayList. Running that results in the following.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//pivot_4.gif" alt="" title="" width="628" height="538" />
</div>

Now you have your customer matrix by means of dynamic column and field referencing. You also a nice example of PIVOT by means of dynamic column names.

This blog probably has about 12 pages worth of explanations but I'd like to leave those to the forums so please follow the directions below if you want to discuss this method further. Otherwise, have fun with it!



\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][2] forum or our [Microsoft SQL Server Admin][3] forum**<ins></ins>

 [1]: http://www.simple-talk.com/community/blogs/andras/archive/2007/09/14/37265.aspx
 [2]: http://forum.ltd.local/viewforum.php?f=17
 [3]: http://forum.ltd.local/viewforum.php?f=22