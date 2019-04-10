---
title: Dealing with The row value(s) updated or deleted either do not make the row unique or they alter multiple rows errors
author: SQLDenis
type: post
date: 2010-12-08T10:08:28+00:00
ID: 966
excerpt: "Someone asked about this error this morning and I decided to turn it into a blog post. Basically the person was using the designer/editor in SSMS to delete a row, the table didn't have a key and the rows were not unique, he got the following error: The&hellip;"
url: /index.php/datamgmt/datadesign/dealing-with-the-row-value-s-updated-or/
views:
  - 36642
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - normalization
  - sql server denali
  - ssms

---
Someone asked about this error [this morning][1] and I decided to turn it into a blog post. Basically the person was using the designer/editor in SSMS to delete a row, the table didn't have a key and the rows were not unique, he got the following error: _The row value(s) updated or deleted either do not make the row unique or they alter multiple rows_. Let's see this in action and also take a look at how we can get around this.

First create the following table.

sql
CREATE TABLE Test(col1 VARCHAR(200));
go
INSERT INTO Test (col1) VALUES('<ccc>ddd</ccc>')
INSERT INTO Test (col1) VALUES('<ccc>ddd</ccc>')
```

Now, do the following, navigate to this table in Object Explorer, right click on the table, select the “Edit top 200 rows” option (see also below for image)

![][2]

Next, pick either of the 2 rows and hit the delete key on your keyboard
  
You will get the following dialog
  
![][3]

Hit yes and you will get the following error dialog

—————————
  
Microsoft SQL Server Management Studio
  
—————————
  
No rows were deleted.

A problem occurred attempting to delete row 1.
  
Error Source: Microsoft.SqlServer.Management.DataTools.
  
Error Message: The row value(s) updated or deleted either do not make the row unique or they alter multiple rows(2 rows).

Correct the errors and attempt to delete the row again or press ESC to cancel the change(s).

This is really a limitation of the designer/tool. So when a human looks at that grid with the 2 rows, the human knows that it wants to delete the first row….however since the 2 rows are completely identical SQL Server does not know which row to delete from the table.

Of course this is also yet another reason to have a primary key on the table.

### Solution

What can we do to resolve this? Easy, stop using the wizard and get familiar with Transact SQL. Open a new query window and then you can use either of these two queries

sql
DELETE TOP (1)
FROM Test
WHERE col1 = '<ccc>ddd</ccc>'
```
The query above uses the TOP operator

sql
SET rowcount 1
   
DELETE
FROM Test
WHERE col1 = '<ccc>ddd</ccc>'
   
SET rowcount 0
```

The query above uses rowcount with a value of 1, this will only affect 1 row now. The set rowcount setting has been put on the deprecation list so be aware of that.

### Who is really at fault here?

I think that both the person who creates a table like this and the tool itself is at fault. The tool is at fault because it should either give you an option of which row to delete somehow or if it knows that it is not unique it should not let you press the delete button.

The person who creates such a table is also at fault because this table doesn't conform to any database design guidelines like normal form. I know this is a simple example in this post, but believe me I have run into these kind of tables in production databases more than a dozen times. 

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][4] forum or our [Microsoft SQL Server Admin][5] forum**<ins></ins>

 [1]: http://stackoverflow.com/questions/4386592/why-cannot-i-delete-edit-a-row-with-xml-value-in-ssms
 [2]: /wp-content/uploads/blogs/DataMgmt/200rows.png ""
 [3]: /wp-content/uploads/blogs/DataMgmt//delete.png ""
 [4]: http://forum.ltd.local/viewforum.php?f=17
 [5]: http://forum.ltd.local/viewforum.php?f=22