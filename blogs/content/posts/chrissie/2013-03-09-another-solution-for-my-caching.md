---
title: Another solution for my caching problem with servicestack.text, dapper and sql server.
author: Christiaan Baes (chrissie1)
type: post
date: 2013-03-09T16:23:00+00:00
ID: 2027
excerpt: How I would like to use servicestack.text, dapper and sql server to use as a cache.
url: /index.php/datamgmt/dbprogramming/another-solution-for-my-caching/
views:
  - 8441
categories:
  - Database Programming
tags:
  - cache
  - redis
  - servicestack.text
  - sql server

---
## Introduction

My problem is the speed of the datastore I am querying. I won&#8217;t name the vendor but their API is horrible. If I want to get all 3000 objects of a certain type I need to first get all the Id&#8217;s from the latest versions of those objects, then I have to get the properties of each of those objects, if I am unlucky those objects have subobjects and then I have to query those too. So to get 3000 objects with 2 subobjects I need to do 9001 queries. Needless to say this will be slow. 

Last week I had [a look at redis][1] to easily cache these objects on my webserver.

Redis is a good solution but it is only production ready on Linux, and our ops-team was not really happy with it. Even though I got permission to set up the linux server on our ESX-servers, I was still looking at other solutions.

Today I might have found such a solution. I can use SQL-server and serialize the objects as json.

## The database

Not really a database since it is just one table.

```tsql
CREATE TABLE [dbo].[objects] (
    [Id]       INT IDENTITY (1, 1) NOT NULL,
    [version]  INT NOT NULL,
    [objid]    INT NOT NULL,
    [language] INT NOT NULL,
    [value]  NVARCHAR(MAX) NOT NULL,
    [objtype] INT NOT NULL, 
    PRIMARY KEY CLUSTERED ([Id] ASC)
);

```
Not sure how this will scale but I am pretty sure that we will not get more than 1 million rows in there any time soon. At the moment it is less than 100k rows.

I can now use [dapper][2] and [servicestack.text][3] to get the data in and out of there.

## The code

```
Imports Dapper
Imports System.Data.SqlClient
Imports ServiceStack.Text

Module Module1

    Sub Main()
        Dim con As New SqlConnection("Server=christiaan-pc;Database=cache;Trusted_Connection=True;")
        con.Open()
        con.Execute("delete from objects")
        Dim object1s = New List(Of object1)
        Dim object2s = New List(Of object2)
        Dim s As New Stopwatch
        Console.WriteLine("Adding to database")
        s.Start()
        For i = 0 To 10000
            object1s.Add(New object1() With {.id = i, .name = "object1_" & i, .version = 1})
            object2s.Add(New object2() With {.id = i, .name = "object2_" & i, .version = 1})
        Next
        Dim objects = New List(Of databaseobject)
        For Each object1 In object1s
            objects.Add(New databaseobject() With {.language = 102, .objid = object1.id, .version = object1.version, .value = object1.SerializeToString(), .objtype = 1})
        Next
        For Each object2 In object2s
            objects.Add(New databaseobject() With {.language = 102, .objid = object2.id, .version = object2.version, .value = object2.SerializeToString(), .objtype = 2})
        Next
        con.Execute("insert objects(objid, version, language, value, objtype) values (@objid, @version,@language,@value,@objtype)",
    objects)
        s.Stop()
        Console.WriteLine(s.ElapsedMilliseconds)
        Console.WriteLine("Fetching from database")
        s.Restart()
        Dim l = con.Query(Of databaseobject)("select objid, version, language, value, objtype from objects where objtype = 1")
        Dim obj1 As object1
        For Each m In l
            obj1 = m.value.FromJson(Of object1)()
        Next
        l = con.Query(Of databaseobject)("select objid, version, language, value, objtype from objects where objtype = 2")
        Dim obj2As object2
        For Each m In l
            obj2  = m.value.FromJson(Of object2)()
        Next
        s.Stop()
        Console.WriteLine(s.ElapsedMilliseconds)
        Console.ReadLine()
    End Sub

    Public Class databaseobject
        Public Property id As Integer
        Public Property objid As Integer
        Public Property version As Integer
        Public Property language As Integer
        Public Property objtype As Integer
        Public Property value As String
    End Class

    Public Class object1
        Public Property name As String
        Public Property id As Integer
        Public Property version As Integer
    End Class

    Public Class object2
        Public Property name As String
        Public Property id As Integer
        Public Property version As Integer
    End Class
End Module
```
So I am adding 2 times 10k objects to the database and after that I rehydrate them again.

On my computer this is the result.

> Adding to database
  
> 13317
  
> Fetching from database
  
> 199

That&#8217;s more then reasonable. Considering it takes 28 seconds to get 3000 objects from the datastore.

## Conclusion

All though I think redis would be a good solution, I don&#8217;t think it is an ideal solution for our situation.

 [1]: /index.php/DataMgmt/DBProgramming/redis-and-vb-net
 [2]: https://code.google.com/p/dapper-dot-net/
 [3]: https://github.com/ServiceStack/ServiceStack.Text