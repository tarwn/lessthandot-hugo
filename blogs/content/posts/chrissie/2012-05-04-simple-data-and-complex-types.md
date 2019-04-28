---
title: 'Simple.Data and complex types: many to one'
author: Christiaan Baes (chrissie1)
type: post
date: 2012-05-04T04:36:00+00:00
ID: 1617
excerpt: In my previous blogpost about Simple.Data I added a table and made a reference to that in my previous table. In other words A person now has an address. I then went on to make a query to join the two together hereby using the explicit join syntax of Simple.Data. If however your database uses foreign keys (which I know it will) then you have no need for a join. Simple.Data just figures it out for itself.
url: /index.php/desktopdev/mstech/simple-data-and-complex-types/
views:
  - 5267
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - complex type
  - simple.data

---
## Introduction

In my [previous blogpost][1] about Simple.Data I added a table and made a reference to that in my previous table. In other words A person now has an address. I then went on to make a query to join the two together hereby using the explicit join syntax of Simple.Data. If however your database uses foreign keys (which I know it will) then you have no need for a join. Simple.Data just figures it out for itself.

In this post I will see about many-to-one objects in essence this mean a poco like this.

```vbnet
Public Class Person
        Public Property LastName As String
        Public Property FirstName As String
        Public Property Address As Address
    End Class

    Public Class Address
        Public Property Street As String
        Public Property HouseNumber As String
    End Class```
## many to one

So for this I add a foreign key to my Person table.

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
            sql = "ALTER TABLE Person ADD CONSTRAINT FK_Person_Address FOREIGN KEY (AddressId) REFERENCES Address(Id)"
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
This code could do with some optimizing but that is of no concern right now, so sorry about that (Hulk will smash you if you do this in production).

We insert the same data as before.

```vbnet
db.Address.Insert(New With {.Street = "street1", .HouseNumber = "1"})
        db.Person.Insert(New With {.LastName = "lastname1", .FirstName = "firstname1", .AddressId = 1})
        db.Person.Insert(New With {.LastName = "lastname1", .FirstName = "firstname2", .AddressId = 1})```
And here we query the result.

```vbnet
Dim result = db.Persons.FindAllByLastName("lastname1")
        For Each person In result
            Console.WriteLine("{0} {1} {2} {3} {4}", person.Id, person.LastName, person.FirstName, person.Address.Street, person.Address.HouseNumber)
        Next```
And like magic We have an Address object.

You will however notice that Address is lazily loaded.

```TSQL
Simple.Data.Ado: 
Text
select [Person].[Id],[Person].[LastName],[Person].[FirstName],[Person].[AddressId] from [Person] WHERE [Person].[LastName] = @p1
@p1 (String) = lastname1

Simple.Data.Ado: 
Text
select [Address].[Id], [Address].[Street], [Address].[HouseNumber] from [Address]  where [Address].[Id] = @p1
@p1 (Int32) = 1

Simple.Data.Ado: 
Text
select [Address].[Id], [Address].[Street], [Address].[HouseNumber] from [Address]  where [Address].[Id] = @p1
@p1 (Int32) = 1

Simple.Data.Ado: 
Text
select [Address].[Id], [Address].[Street], [Address].[HouseNumber] from [Address]  where [Address].[Id] = @p1
@p1 (Int32) = 1

Simple.Data.Ado: 
Text
select [Address].[Id], [Address].[Street], [Address].[HouseNumber] from [Address]  where [Address].[Id] = @p1
@p1 (Int32) = 1
```
I get 4 queries to our address table. This might be desired in some cases, but in this case I just know I will need the address right away and lazy loading is just adding extra IO. This is easily solved.

```
Dim result = db.Persons.FindAllByLastName("lastname1").WithAddress```
Now I get this query instead.

```tsql
select [Person].[Id],[Person].[LastName],[Person].[FirstName],[Person].[AddressId],[Address].[Id] AS [__with1__Address__Id],[Address].[Street] AS [__with1__Address__Street],[Address].[HouseNumber] AS [__with1__Address__HouseNumber] from [Person] LEFT JOIN [Address] ON ([Address].[Id] = [Person].[AddressId]) WHERE [Person].[LastName] = @p1
@p1 (String) = lastname1```
which is much better since it solves our select N+1 problem we were having.

## Conclusion

It is very easy to query and get a nice poco in return with a many to one relationship.
  
And now we wait for Eli&#8217;s PetaPoco reply 😉

 [1]: /index.php/DesktopDev/MSTech/more-simple-data-with-vb