---
title: 'More Simple.Data with VB.Net: adding fields and tables'
author: Christiaan Baes (chrissie1)
type: post
date: 2012-05-01T10:06:00+00:00
ID: 1611
excerpt: |
  So yesterday I did a post about Simple.Data and Eli did a post about Petapoco based on what I did. And Eli commented that I did not have a primary key in my table.
  Today I will be adding tables and columns to my database.
url: /index.php/desktopdev/mstech/more-simple-data-with-vb/
views:
  - 6285
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - simple.data
  - vb.net

---
## Introduction

So yesterday I did [a post about Simple.Data][1] and Eli did [a post about Petapoco][2] based on what I did. And Eli commented that I did not have a primary key in my table.
  
In this post I will add that primary key and another table.

## Adding a column

Adding a column should never be a big problem. But with Simple.Data it&#8217;s just no pain what so ever. Most ORM&#8217;s will require you to add a property to your model and then something to the mapping file. With Simple.Data the column is just available.

If I change the create table function to this.

```vbnet
Private Sub CreateTablePerson()
        Using cn = New SqlCeConnection(ConnectionString)
            If cn.State = ConnectionState.Closed Then
                cn.Open()
            End If
            Dim sql = "create table Person (Id int IDENTITY(1,1) PRIMARY KEY, LastName nvarchar (40) not null, FirstName nvarchar (40))"
            Using cmd = New SqlCeCommand(sql, cn)
                cmd.ExecuteNonQuery()
            End Using
        End Using
    End Sub```
I can now simply change my code to read the Id.

```vbnet
Sub Main()
        CreateDatabase()
        CreateTablePerson()
        Dim db = Simple.Data.Database.OpenConnection(ConnectionString)
        db.Person.Insert(New With {.LastName = "lastname1", .FirstName = "firstname1"})
        db.Person.Insert(New With {.LastName = "lastname1", .FirstName = "firstname2"})
        Dim result = db.Persons.FindAllByLastName("lastname1")
        For Each person In result
            Console.WriteLine("{0} {1} {2}", person.Id, person.LastName, person.FirstName)
        Next
        Dim count = db.Persons.FindAllByLastName("lastname1").Count
        Console.WriteLine("Count: " & count)
        Console.ReadLine()
    End Sub```
So adding a column is not a problem.

## Adding a table

So lets change our Person table a bit and add an address table.

```vbnet
Private Sub CreateTablePerson()
        Using cn = New SqlCeConnection(ConnectionString)
            If cn.State = ConnectionState.Closed Then
                cn.Open()
            End If
            Dim sql = "create table Person (Id int IDENTITY(1,1) PRIMARY KEY, LastName nvarchar (40) not null, FirstName nvarchar (40), AddressId int not null)"
            Using cmd = New SqlCeCommand(sql, cn)
                cmd.ExecuteNonQuery()
            End Using
        End Using
    End Sub

    Private Sub CreateTableAddress()
        Using cn = New SqlCeConnection(ConnectionString)
            If cn.State = ConnectionState.Closed Then
                cn.Open()
            End If
            Dim sql = "create table Address (Id int IDENTITY(1,1) PRIMARY KEY, Street nvarchar (40) not null, HouseNumber nvarchar (10))"
            Using cmd = New SqlCeCommand(sql, cn)
                cmd.ExecuteNonQuery()
            End Using
        End Using
    End Sub```
And now I can change my main method to this.

```vbnet
Sub Main()
        CreateDatabase()
        CreateTableAddress()
        CreateTablePerson()
        Dim db = Simple.Data.Database.OpenConnection(ConnectionString)
        db.Address.Insert(New With {.Street = "street1", .HouseNumber = "1"})
        db.Person.Insert(New With {.LastName = "lastname1", .FirstName = "firstname1", .AddressId = 1})
        db.Person.Insert(New With {.LastName = "lastname1", .FirstName = "firstname2", .AddressId = 1})
        Dim result = db.Persons.FindAllByLastName("lastname1")
        For Each person In result
            Console.WriteLine("Person: {0} {1} {2}", person.Id, person.LastName, person.FirstName)
            Dim address = db.Address.FindById(person.AddressId)
            Console.WriteLine("Address: {0} {1}", address.Street, address.HouseNumber)
        Next
        Dim count = db.Persons.FindAllByLastName("lastname1").Count
        Console.WriteLine("Count: " & count)
        Console.ReadLine()
    End Sub```
in the above I am doing it all wrong of course since I am creating a query to the address table for each of the rows in the persons. I think they call that a select N+1. I could and should do all this with one query.

And I can, kinda. But there is a catch.

First I will show you what works in C#

```csharp
var result = db.Persons.Query()
                .Join(db.Address).On(db.Persons.AddressId == db.Address.Id)
                .Select(db.Persons.LastName, db.Persons.FirstName, db.Address.Street, db.Address.HouseNumber);
            foreach(var person in result)
            {
                Console.WriteLine("Person: {0} {1}", person.LastName, person.FirstName);
                Console.WriteLine("Address: {0} {1}", person.Street, person.HouseNumber);
            }```
And that gives this SQL

```tsql
select [Person].[LastName],[Person].[FirstName],[Address].[Street],[Address].[HouseNumber] from [Person] JOIN [Address] ON ([Person].[AddressId] = [Address].[Id])```
So no more Select N+1. Oh joy.

Now we try the same thing in VB.Net.

```vbnet
Dim result = db.Persons.Query _
                .Join(db.Address).On(db.Persons.AddressId = db.Address.Id) _
                .Select(db.Persons.LastName, db.Persons.FirstName, db.Address.Street, db.Address.HouseNumber)
        For Each person In result
            Console.WriteLine("Person: {0} {1}", person.LastName, person.FirstName)
            Console.WriteLine("Address: {0} {1}", person.Street, person.HouseNumber)
        Next```
And grumble, grumble, curse, curse, swearword, swearword. It doesn&#8217;t work. It should, but it doesn&#8217;t. No really it doesn&#8217;t.

You get this errormessage.

> Public member &#8216;Address&#8217; on type &#8216;Database&#8217; not found.

But I swear it should work. 

So an hour or so later and trying other things. I get an idea.

```vbnet
Dim address = db.Address
        Dim result = db.Persons.Query _
                .Join(address).On(db.Persons.AddressId = address.Id) _
                .Select(db.Persons.LastName, db.Persons.FirstName, address.Street, address.HouseNumber)
        For Each person In result
            Console.WriteLine("Person: {0} {1}", person.LastName, person.FirstName)
            Console.WriteLine("Address: {0} {1}", person.Street, person.HouseNumber)
        Next```
And all of a sudden it works. I have yet to find a reason why the VB compiler doesn&#8217;t like it but I will look into it further when I have some time.

## Conclusion

You can add column easily and you can do joins quite easily. VB.Net however doesn&#8217;t seem to like it directly but there is a work around.

 [1]: /index.php/DesktopDev/MSTech/simple-data-and-vb-net
 [2]: /index.php/DesktopDev/MSTech/CSharp/playing-with-petapoco