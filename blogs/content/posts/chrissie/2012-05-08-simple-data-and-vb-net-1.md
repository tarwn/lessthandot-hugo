---
title: Simple.Data and VB.Net The final queries
author: Christiaan Baes (chrissie1)
type: post
date: 2012-05-08T06:00:00+00:00
ID: 1621
excerpt: In this last post I want to show some more complex queries. But I also want to remind you that you need to know when to quit. When queries get to complex you will have to jump through hoops to get them working with any ORM while it is all very "easy" to do in SQL. In other words use the right tool for the job.
url: /index.php/desktopdev/mstech/simple-data-and-vb-net-1/
views:
  - 4887
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - simple.data
  - vb.net

---
## Introduction

In this last post I want to show some more complex queries. But I also want to remind you that you need to know when to quit. When queries get to complex you will have to jump through hoops to get them working with any ORM while it is all very &#8220;easy&#8221; to do in SQL. In other words use the right tool for the job.

## Setup

Here is the code to set everything up.

```vbnet
Private Sub InsertTestData(ByVal db As Object)
        db.Address.Insert(New With {.Street = "street1", .HouseNumber = "1"})
        db.Address.Insert(New With {.Street = "street2", .HouseNumber = "2"})
        db.Person.Insert(New With {.LastName = "lastname1", .FirstName = "firstname1", .AddressId = 1})
        db.Person.Insert(New With {.LastName = "lastname1", .FirstName = "firstname2", .AddressId = 1})
        db.Person.Insert(New With {.LastName = "lastname2", .FirstName = "firstname3", .AddressId = 2})
        db.BadHabit.Insert(New With {.BadHabit = "Drinks"})
        db.BadHabit.Insert(New With {.BadHabit = "Smokes"})
        db.BadHabit.Insert(New With {.BadHabit = "Eats to much"})
        db.BadHabitPerson.Insert(New With {.BadHabitId = 1, .PersonId = 1})
        db.BadHabitPerson.Insert(New With {.BadHabitId = 2, .PersonId = 1})
        db.BadHabitPerson.Insert(New With {.BadHabitId = 3, .PersonId = 2})
        db.BadHabitPerson.Insert(New With {.BadHabitId = 3, .PersonId = 1})
        db.BadHabitPerson.Insert(New With {.BadHabitId = 3, .PersonId = 3})
    End Sub

    Public Function ConnectionString() As String
        Return "DataSource=""test.sdf""; Password=""mypassword"""
    End Function

    Private Function CreateDatabase() As SqlCeEngine
        If File.Exists("test.sdf") Then File.Delete("test.sdf")
        Dim en = New SqlCeEngine(ConnectionString)
        en.CreateDatabase()
        Return en
    End Function

    Private Sub CreateTables()
        Using cn = New SqlCeConnection(ConnectionString)
            If cn.State = ConnectionState.Closed Then
                cn.Open()
            End If
            For Each SqlScript In CreateTableScripts()
                Using cmd = New SqlCeCommand(SqlScript, cn)
                    cmd.ExecuteNonQuery()
                End Using
            Next
        End Using
    End Sub

    Private Function CreateTableScripts() As IList(Of String)
        Dim s As New List(Of String)
        s.Add("CREATE TABLE Address (Id int IDENTITY(1,1) PRIMARY KEY, Street nvarchar (40) NOT NULL, HouseNumber nvarchar (10))")
        s.Add("CREATE TABLE Person (Id int IDENTITY(1,1) PRIMARY KEY, LastName nvarchar (40) NOT NULL, FirstName nvarchar (40), AddressId int NOT NULL)")
        s.Add("ALTER TABLE Person ADD CONSTRAINT FK_Person_Address FOREIGN KEY (AddressId) REFERENCES Address(Id)")
        s.Add("CREATE TABLE BadHabit (Id int IDENTITY(1,1) PRIMARY KEY, BadHabit nvarchar (40) NOT NULL)")
        s.Add("CREATE TABLE BadHabitPerson (BadHabitId int NOT NULL, PersonId int NOT NULL)")
        s.Add("ALTER TABLE BadHabitPerson ADD PRIMARY KEY(BadHabitId, PersonId)")
        s.Add("ALTER TABLE BadHabitPerson ADD CONSTRAINT FK_BadHabitPerson_Person FOREIGN KEY (PersonId) REFERENCES Person(Id)")
        s.Add("ALTER TABLE BadHabitPerson ADD CONSTRAINT FK_BadHabitPerson_BadHabit FOREIGN KEY (BadHabitId) REFERENCES BadHabit(Id)")
        Return s
    End Function```
## queries

To get the person with the streetname &#8220;street1&#8221; you can should do this in C#

```csharp
db.Persons.FindAll(db.Persons.Address.Street == "street1");```
The above however does not work in VB.Net.

But this does.

```vbnet
db.Person.FindAll(db.Address.Street = "street1").WithAddress```
```tsql
select [Person].[Id],[Person].[LastName],[Person].[FirstName],[Person].[AddressId],[Address].[Id] AS [__with1__Address__Id],[Address].[Street] AS [__with1__Address__Street],[Address].[HouseNumber] AS [__with1__Address__HouseNumber] from [Person] LEFT JOIN [Address] ON ([Address].[Id] = [Person].[AddressId]) WHERE [Address].[Street] = @p1
@p1 (String) = street1```
The above works but just because I use WithAddress and setup referential integrity. 

I can also use like. Like this.

```vbnet
db.Person.FindAll(db.Address.Street.Like("street%")).WithAddress```
```tsql
select [Person].[Id],[Person].[LastName],[Person].[FirstName],[Person].[AddressId],[Address].[Id] AS [__with1__Address__Id],[Address].[Street] AS [__with1__Address__Street],[Address].[HouseNumber] AS [__with1__Address__HouseNumber] from [Person] LEFT JOIN [Address] ON ([Address].[Id] = [Person].[AddressId]) WHERE [Address].[Street] LIKE @p1
@p1 (String) = street%```
I can also select which columns I want to return.

```vbnet
db.Person.All.Select(db.Person.LastName)```
```tsql
select [Person].[LastName] from [Person]```
I can also give my columns aliases.

```vbnet
db.Person.All.Select(db.Person.LastName.As("Name"))```
```tsql
select [Person].[LastName] AS [Name] from [Person]```
And you can do much more.
  
But I think I have learned enough about Simple.Data now. And it&#8217;s time to move on to something else.

## Conclusion

I really like Simple.Data and I will surely use it again in the future. But if you want to keep your sanity I would recommend using C# and not VB.Net. The whole dynamic thing is very quirky in VB.Net.

You should however know the limits of the framework you are using and keep in mind that some things are just better solved another way. It will give you less headaches by trying to fit a square peg in a round hole and it will make for a more enjoyable experience all around. I see way to many people trying to use whatever they have to do whatever they need to do and then hit a brick wall. So stop doing that and use Simple.Data for what it was supposed to do quick and easy CRUD.