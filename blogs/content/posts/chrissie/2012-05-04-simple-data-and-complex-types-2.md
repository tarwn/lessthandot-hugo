---
title: 'Simple.Data and complex types: many to many'
author: Christiaan Baes (chrissie1)
type: post
date: 2012-05-04T09:18:00+00:00
ID: 1619
excerpt: After doing the blogposts about One to many and many to one it is now time to do one one about many to many with Simple.Data.
url: /index.php/desktopdev/mstech/simple-data-and-complex-types-2/
views:
  - 2463
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - complex type
  - many to many
  - simple.data

---
## Introduction

After doing the blogposts about One to many and [many to one][1] it is now time to do one one about many to many with Simple.Data.

## Many to many

It&#8217;s time to change our design. It is clear that our data is not normalized like it should. I need an intermediate table between Person and BadHabit.

Here is the code to do that.

```vbnet
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
And we have to add some data to make this work.

```vbnet
Dim db = Simple.Data.Database.OpenConnection(ConnectionString)
        db.Address.Insert(New With {.Street = "street1", .HouseNumber = "1"})
        db.Person.Insert(New With {.LastName = "lastname1", .FirstName = "firstname1", .AddressId = 1})
        db.Person.Insert(New With {.LastName = "lastname1", .FirstName = "firstname2", .AddressId = 1})
        db.BadHabit.Insert(New With {.BadHabit = "Drinks"})
        db.BadHabit.Insert(New With {.BadHabit = "Smokes"})
        db.BadHabit.Insert(New With {.BadHabit = "Eats to much"})
        db.BadHabitPerson.Insert(New With {.BadHabitId = 1, .PersonId = 1})
        db.BadHabitPerson.Insert(New With {.BadHabitId = 2, .PersonId = 1})
        db.BadHabitPerson.Insert(New With {.BadHabitId = 3, .PersonId = 2})
```
You will now notice that our previous query no longer works.

this line 

```vbnet
person.BadHabit```
Will now give you.

> Public member &#8216;BadHabit&#8217; on type &#8216;SimpleRecord&#8217; not found.

This can easily be fixed by adding the intermediarie table in the mix.

```vbnet
Dim result = db.Persons.FindAllByLastName("lastname1").WithAddress
        For Each person In result
            Console.WriteLine("{0} {1} {2} {3} {4}", person.Id, person.LastName, person.FirstName, person.Address.Street, person.Address.HouseNumber)
            For Each BadHabit In person.BadHabitPerson.BadHabit
                Console.WriteLine(BadHabit.BadHabit)
            Next
        Next```
See how I added BadHabitPerson in between Person and BadHabit? That makes it work.

The sql is now.

```tsql
Simple.Data.Ado: 
Text
select [Person].[Id],[Person].[LastName],[Person].[FirstName],[Person].[AddressId],[Address].[Id] AS [__with1__Address__Id],[Address].[Street] AS [__with1__Address__Street],[Address].[HouseNumber] AS [__with1__Address__HouseNumber] from [Person] LEFT JOIN [Address] ON ([Address].[Id] = [Person].[AddressId]) WHERE [Person].[LastName] = @p1
@p1 (String) = lastname1

Simple.Data.Ado: 
Text
select [BadHabit].[Id],[BadHabit].[BadHabit] from [BadHabit] JOIN [BadHabitPerson] ON ([BadHabit].[Id] = [BadHabitPerson].[BadHabitId]) WHERE [BadHabitPerson].[PersonId] = @p1
@p1 (Int32) = 1

Simple.Data.Ado: 
Text
select [BadHabit].[Id],[BadHabit].[BadHabit] from [BadHabit] JOIN [BadHabitPerson] ON ([BadHabit].[Id] = [BadHabitPerson].[BadHabitId]) WHERE [BadHabitPerson].[PersonId] = @p1
@p1 (Int32) = 2```
Which I would like to have in one statement again.
  
I could however not get it in one statement. But I&#8217;m sure Mark will fix it one day ;-).

## Conclusion

A bit less intuitive but it&#8217;s still pretty simple once you know how.

I have the complete code in a gist here. 

https://gist.github.com/2594192

 [1]: /index.php/DesktopDev/MSTech/simple-data-and-complex-types