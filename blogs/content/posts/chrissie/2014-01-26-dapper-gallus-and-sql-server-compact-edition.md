---
title: Dapper, Gallus and SQl server compact edition
author: Christiaan Baes (chrissie1)
type: post
date: 2014-01-26T08:48:57+00:00
ID: 2325
excerpt: 'Sql server compact edition gives less than clear error messages when you use multiple statements in your command. '
url: /index.php/desktopdev/mstech/vbnet/dapper-gallus-and-sql-server-compact-edition/
views:
  - 10707
categories:
  - VB.NET
tags:
  - Dapper
  - Gallus
  - vb.net

---
When I was writing my <a href="/index.php/desktopdev/mstech/vbnet/gallus-a-slightly-different-dapper/" title="First blogpost about Gallus" target="_blank">blogpost yesterday about Gallus</a> I started by using SQL server compact edition. Because it is easy to use and more or less compatible with sql server.

And I found out that for my case sql server compact edition was less compatible. SQL server compact edition does not support multiple statements in one command. Which is what we do when we use QueryMultiple in dapper. 

And you will happy to know that the person who wrote the errormessage doesn&#8217;t really care about you as the user. 

<div id="attachment_2326" style="width: 310px" class="wp-caption alignnone">
  <a href="/wp-content/uploads/2014/01/sqlce.png"><img src="/wp-content/uploads/2014/01/sqlce-300x52.png" alt="Exception thrown by QueryMultiple" width="300" height="52" class="size-medium wp-image-2326" srcset="/wp-content/uploads/2014/01/sqlce-300x52.png 300w, /wp-content/uploads/2014/01/sqlce.png 903w" sizes="(max-width: 300px) 100vw, 300px" /></a>
  
  <p class="wp-caption-text">
    Exception thrown by QueryMultiple
  </p>
</div>

> There was an error parsing the query. [ Token line number = 2,Token line offset = 1,Token in error = select ]

Took me a few minutes to figure out what he really meant. 

The Gallus code you will be glad to know worked just fine since it uses only one sql statement.

If you want to try this just nuget sql server compact edition into your project and change these methods in your Setupdatabase module.

```vbnet
Function Setup(withTestData As Boolean) As IDbConnection
        CreateDatabase()
        Dim db = New SqlCeConnection("DataSource=""test.sdf""; Password=""mypassword""")
        db.Open()
        CreateTables(db)
        If withTestData Then InsertTestData(db)
        Return db
    End Function
```
And 

```vbnet
Private Function CreateDatabase() As SqlCeEngine
        If File.Exists("test.sdf") Then File.Delete("test.sdf")
        Dim en = New SqlCeEngine("DataSource=""test.sdf""; Password=""mypassword""")
        en.CreateDatabase()
        Return en
    End Function
```
I&#8217;m just leaving this here for my future self.