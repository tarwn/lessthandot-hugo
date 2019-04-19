---
title: Using one Parameter for Multiple Datasets in Reporting Services
author: Ted Krueger (onpnt)
type: post
date: 2009-05-11T15:28:56+00:00
ID: 422
url: /index.php/datamgmt/datadesign/using-one-parameter-for-multiple-dataset/
views:
  - 18067
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Microsoft SQL Server

---
Here is a nice little tip that I noticed is often overlooked or just not known.

When you have a report that has several datasets supplying multiple objects in the report and these objects are linked based on the selection of parameters in a parent object, you do not need to create multiple parameters and set them with expressions to the parent parameteres. All you need to do is write your procedures and reports to use the same name across the board.

Here is what I mean by that.

Say you have your first procedure like this

```sql
Create Procedure FindOrders(@date datetime,@plant smallint)
As
Select 
 OrderNum
 ,OrderDate
From
Orders
Where 
Convert(varchar(10),OrderDate,121) = Convert(varchar(10),@date,121) 
and
plant = @plant
Go
```
You would then call it as a Text statement in the DS as
  
Exec FindOrders @date,@plant 

This would be a typical example of searching orders. The users then ask for a cross reference table of orders shipped in the same day. Instead of adding a column to the table, they want an actual table next to the other.

Like this layout 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//parms_ssrs_2.gif" alt="" title="" width="835" height="292" />
</div>

Most would be inclined to create another procedure and then create matching parameters for that report. Then use the “Parameters!” call to assign a default value to the parameter going to the second procedure. You can bypass this by simply naming the parameters in the procedures the same and then call them in your dataset as Text and using the same names.

So your second procedure would be something like

```sql
Create Procedure FindShippedOrders(@date datetime,@plant smallint)
As
Select 
 shipdate
 ,OrderNum
From
Shipping
Where 
Convert(varchar(10),shipdate,121) = Convert(varchar(10),@date,121) 
and
plant = @plant
Go
```
You would call this as
  
Exec FindShippedOrders @date,@plant 

Now when you check the report parameters window you will still only see two parameters. Run your report and you will see the parameters load while working with both datasets.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//parms_ssrs.gif" alt="" title="" width="819" height="330" />
</div>