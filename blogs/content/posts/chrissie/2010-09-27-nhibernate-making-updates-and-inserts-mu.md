---
title: nHibernate making updates and inserts much faster
author: Christiaan Baes (chrissie1)
type: post
date: 2010-09-27T09:06:48+00:00
ID: 908
url: /index.php/desktopdev/mstech/nhibernate-making-updates-and-inserts-mu/
views:
  - 13328
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - nhibernate
  - vb.net

---
## Introduction

The model behind this screen is pretty complex. 

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/NHibernate/nhib1.png" alt="" title="" width="1208" height="832" />
</div>

This part is especially bad. 

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/NHibernate/nhib2.png" alt="" title="" width="467" height="182" />
</div>

Each of those colored lines is 421 rows in the database and this particular one has 14 of those. So that means it has to load 5894 rows just for that little thing. But that is not the big problem since it can do that quit fast and with one select statement.
  
But some of the time you also need to save it to the database and that is slow since that needs 5894 statements.

Did you notice all the Update table &#8230; where id = anumber. EAch one of those is a roundtrip to the server.

## Not optimized

This is what happens when I save the complete object to the database. (I used [NHProf][1] to visualize what was wrong). 

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/NHibernate/nhib4.png" alt="" title="" width="1024" height="768" />
</div>

It has a lot of statements it has to execute to save this thing to the database. There is nothing we can do about the number of statements but we can batch some of them so that the number of round trips to the server are limited. 

## Optimized

First of all you have to add this line to the NHibernate configuration. I do my configuration via code but you can do this via XML also.

```vbnet
_Configuration.SetProperty("adonet.batch_size", "1")```
And here is my complete configuration.

```vbnet
_Configuration = New Configuration()
            _Configuration.SetProperty(Environment.ConnectionProvider, SolutionSettings.Configuration.NHibernate.ConnectionProvider)
            _Configuration.SetProperty(Environment.Dialect, SolutionSettings.Configuration.NHibernate.Dialect)
            _Configuration.SetProperty(Environment.ConnectionDriver, SolutionSettings.Configuration.NHibernate.ConnectionDriver)
            _Configuration.SetProperty(Environment.ConnectionString, String.Format(SolutionSettings.Configuration.NHibernate.ConnectionString, SolutionSettings.Configuration.Server.DatabaseServer, SolutionSettings.Configuration.Database.Test, "Database"))
            _Configuration.SetProperty(Environment.ShowSql, "false")
            _Configuration.SetProperty(Environment.CommandTimeout, "100000")
            Dim i = New NHibernate.Bytecode.LinFu.ProxyFactoryFactory
            _Configuration.SetProperty(Environment.ProxyFactoryFactoryClass, "NHibernate.ByteCode.LinFu.ProxyFactoryFactory, NHibernate.ByteCode.LinFu")
            _Configuration.SetProperty("generate_statistics", "true")
            _Configuration.SetProperty("adonet.batch_size", "1")```
And you have to add a SetBatchSize to your session.
  
Like this.

```vbnet
_Session.SetBatchSize(50)
                _Session.SaveOrUpdate(SingleObject)
                _Session.Flush()
                ```
And this is the result.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/NHibernate/nhib3.png" alt="" title="" width="1024" height="768" />
</div>

It now only takes 4 seconds to save and only 194 roundtrips to the server. Compared to the 45 seconds and 6004 roundtrips that is a big improvement. 

You will see that it batches those update statement together.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/NHibernate/nhib5.png" alt="" title="" width="723" height="64" />
</div>

It now only takes 14ms for 50 statements to complete. While it used to take 50*6 = 300ms in the none batched version.

## Conclusion

if you notice that update or insert are slow when you use nHibernate and you use huge collections then you better set the ado.Net batchsize. 

<span class="MT_smaller">Only 4 more posts until the 250th and final post.</span>

 [1]: http://nhprof.com/