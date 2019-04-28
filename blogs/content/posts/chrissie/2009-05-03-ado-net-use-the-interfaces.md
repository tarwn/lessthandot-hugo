---
title: ADO.Net use the interfaces
author: Christiaan Baes (chrissie1)
type: post
date: 2009-05-03T11:45:00+00:00
ID: 408
url: /index.php/desktopdev/mstech/ado-net-use-the-interfaces/
views:
  - 2137
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - ado.net
  - vb.net

---
I see a lot of people doing this.

```vb.net
Dim con As New System.Data.SqlClient.SqlConnection
        con.ConnectionString = "..."
        con.Open()
        Dim command As New System.Data.SqlClient.SqlCommand
        command.Connection = con
        command.CommandText = "update tbl set col = 'something'"
        command.ExecuteNonQuery()
        command.Dispose()
        con.Dispose()
```
I like to use the interfaces instead and use the factory methods that come with it. Something like this.

```vbnet
Dim con As System.Data.IDbConnection = New System.Data.SqlClient.SqlConnection
        con.ConnectionString = "..."
        con.Open()
        Dim command As System.Data.IDbCommand = con.CreateCommand
        command.Connection = con
        command.CommandText = "update tbl set col = 'something'"
        command.ExecuteNonQuery()
        command.Dispose()
        con.Dispose()```
this way if we ever have to change to an other database we just have to change the = New System.Data.SqlClient.SqlConnection. Of course we would just get a connection from a central place and then we only have to change that little piece of code once. 

IDbCommand also has a factory method to create parameters. Since you are using an sqlconnection the factory methods will create sql implementation classes which are optimized to work with MS-SQL-Server.

And even then we have to hope that the databases understand the same dialect 😉

SO it&#8217;s not a perfect solution but it can help you on the way to create a more maintainable application.