---
title: Dealing with the “The text, ntext, or image pointer value conflicts with the column name specified.” error from Visual Foxpro
author: Naomi Nosonovsky
type: post
date: 2012-04-03T22:58:00+00:00
ID: 1569
excerpt: |
  Recently my colleague sent me a strange error she was getting in our VFP application
  "The text, ntext, or image pointer value conflicts with the column name specified."
  
  I was not getting the same error, so it was puzzling indeed. After some research&hellip;
url: /index.php/datamgmt/datadesign/dealing-with-the-the-text/
views:
  - 14273
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
Recently my colleague sent me a strange error she was getting in our VFP application
  
&#8220;The text, ntext, or image pointer value conflicts with the column name specified.&#8221;

I was not getting the same error, so it was puzzling indeed. After some research online I found the [following thread][1] and suspected the problem was in the image size. And yes, after I chose a file about 2MB in size I was able to reproduce the same error. I haven&#8217;t been able to determine the exact image size when this error occurs, but in my tests the files less than 400Kb worked and bigger files produced the error.

This is how our VFP application is structured. We use SPT for data retrieval, then using an approach similar to described in [this FAQ][2] we make this cursor updateable and then send updates using TABLEUPDATE function. We&#8217;re also using DSN and old SQL Server driver. In the table that was giving this error the field for storing images is varbinary(max).

I tested a similar form where the table used an image column. The same error didn&#8217;t occur and I was able to import 2MB files without a problem.

So, we now determine an internal SQL Server bug (most likely in the old SQL Server driver) that can not work with big varbinary(max) columns by using remote views form Visual FoxPro.
  
I also found a thread on a [similar topic][3] by Cetin Basoz at foxite.com forum.

Luckily there was a relatively simple solution for this problem: use direct updates/inserts with parameters instead of remote views. So, in my forms I was checking for big size images and used a different approach when saving data.

Here is a test program that can demonstrate the problem:

```
ON ERROR 
CLEAR ALL
CLEAR 
oAppObj = NEWOBJECT('my_appObj','my_Class.vcx')
=CURSORSETPROP('MapBinary',.t.,0)
WAIT WINDOW NOWAIT NOCLEAR 'Connecting to server data ...'

      SQLSETPROP(0, "ConnectTimeOut", 15)
      SQLSETPROP(0, "DispWarnings", .F.)
      SQLSETPROP(0, "DispLogin", 3)  && disable login dialog if there is a problem
      SQLHandle=SQLCONNECT('MyDB', 'userid', 'password')
      sqlexec(SQLHandle, 'use MyDB' )   

lcPicFile = GETFILE('jpg;png')

lcStr = CAST(FILETOSTR(m.lcPicFile)  as blob)       

TEXT TO lcSQL TEXTMERGE NOSHOW 
update dbo.rsSeatMaps set SeatMap = ?m.lcStr
where SeatMapID = 10
ENDTEXT 

?SQLEXEC(SQLHandle, m.lcSQL)
aError(laError)
DISPLAY MEMORY LIKE laError

? SQLEXEC(SQLHandle,'select * from dbo.rsSeatMaps where SeatMapID = 10','rsSeatMaps')
?make_view_updatable('rsSeatMaps',5, SQLHandle)

replace SeatMap WITH m.lcStr IN rsSeatMaps

?TABLEUPDATE(.t.,.t., 'rsSeatMaps')
?AERROR(laError)
DISPLAY MEMORY LIKE laError
SQLDISCONNECT(0)
WAIT CLEAR
```

The first direct update command succeeds, but the second remote view update fails.

Hopefully this blog post will be helpful.

 [1]: http://social.msdn.microsoft.com/Forums/en/transactsql/thread/d26e2143-630b-46e7-a81b-a33a24af20ce
 [2]: http://www.universalthread.com/ViewPageNewFAQ.aspx?ID=8153
 [3]: http://www.foxite.com/archives/warning-possible-bug-sql-server-0000239346.htm