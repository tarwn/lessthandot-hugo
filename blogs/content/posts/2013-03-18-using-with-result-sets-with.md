---
title: Using WITH RESULT SETS with stored procedures that return multiple resultsets
author: SQLDenis
type: post
date: 2013-03-18T21:07:00+00:00
ID: 2037
excerpt: |
  Yesterday I blogged about using WITH RESULT SETS with the EXECUTE command here: Use WITH RESULT SETS to change column names and datatypes of a resultset. Naomi Nosonovsky left the following comment
  Can you use this feature to get result sets from the s&hellip;
url: /index.php/datamgmt/dbprogramming/using-with-result-sets-with/
views:
  - 8592
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - how to
  - sql server 2012

---
Yesterday I blogged about using WITH RESULT SETS with the EXECUTE command here: [Use WITH RESULT SETS to change column names and datatypes of a resultset][1]. Naomi Nosonovsky left the following comment

> Can you use this feature to get result sets from the stored procedure returning multiple result sets such as sp_spaceused? If so, can you show this?

So today we are going to look at how we can do this. Let&#8217;s say we execute the following stored procedure without specifying an object name

sql
EXEC sp_spaceused
```

You will get two resultsets, the output will look something like this 

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/SQL2013/sp_spaceused_resultset.PNG?mtime=1363647770"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/SQL2013/sp_spaceused_resultset.PNG?mtime=1363647770" width="347" height="220" /></a>
</div>

Now let&#8217;s see what happens if we only specify the first result set

sql
EXEC sp_spaceused 
WITH RESULT SETS
( 
   (
   DBNAme nvarchar(100),
   DatabaseSize nvarchar(1000),
   UnollactedSpace nvarchar(100)
   )

);
```

Here is the output

_Msg 11535, Level 16, State 1, Procedure sp_spaceused, Line 128
  
EXECUTE statement failed because its WITH RESULT SETS clause specified 1 result set(s), and the statement tried to send more result sets than this._

As you can see both result sets have to be accounted for when using WITH RESULT SETS
  
Adding the second result set will fix this, run the following

sql
EXEC sp_spaceused 
WITH RESULT SETS
( 
   (
   DatabaseNAme nvarchar(100),
   DatabaseSize nvarchar(1000),
   UnollactedSpace nvarchar(100)
   ),
   (
   Reserved nvarchar(100),
   Data nvarchar(1000),
   IndexSize nvarchar(100),
   Unused nvarchar(100)
   )
);
```

Adding the second result set fixed it, it all works now and the column names are the ones we have specified

 [1]: /index.php/DataMgmt/DBProgramming/MSSQLServer/use-with-result-sets-to