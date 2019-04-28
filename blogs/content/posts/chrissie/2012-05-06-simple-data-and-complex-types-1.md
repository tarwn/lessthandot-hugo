---
title: 'Simple.Data and complex types: one to many'
author: Christiaan Baes (chrissie1)
type: post
date: 2012-05-06T05:48:00+00:00
ID: 1618
excerpt: In my previous post I described how to deal with complex types and many to one dependencies now I will do the on to many relationships.
url: /index.php/desktopdev/mstech/simple-data-and-complex-types-1/
views:
  - 5295
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - complex type
  - one to many
  - simple.data

---
## Introduction

In my previous post I described how to deal with complex types and many to one dependencies now I will do the one to many relationships.

For this I will add a collection to our Person.

```vbnet
Public Class Person
        Public Property LastName As String
        Public Property FirstName As String
        Public Property Address As Address
        Public Property BadHabits As IList(Of BadHabit)
    End Class

    Public Class Address
        Public Property Street As String
        Public Property HouseNumber As String
    End Class

    Public Class BadHabit
        Public Property BadHabit As String
    End Class```
## one to many

For this we first have to add a table to our database.

```vbnet
Private Sub CreateTableBadHabits()
        Using cn = New SqlCeConnection(ConnectionString)
            If cn.State = ConnectionState.Closed Then
                cn.Open()
            End If
            Dim sql = "create table BadHabit (Id int IDENTITY(1,1) PRIMARY KEY, BadHabit nvarchar (40) not null, PersonId int)"
            Using cmd = New SqlCeCommand(sql, cn)
                cmd.ExecuteNonQuery()
            End Using
            sql = "ALTER TABLE BadHabit ADD CONSTRAINT FK_BadHabit_Person FOREIGN KEY (PersonId) REFERENCES Person(Id)"
            Using cmd = New SqlCeCommand(sql, cn)
                cmd.ExecuteNonQuery()
            End Using
        End Using
    End Sub```
We will better normalize it later when we have some time ;-). As you can see I also added the foreign key. I will leave the index creation up to you.

And we also need some more testdata.

```vbnet
db.Address.Insert(New With {.Street = "street1", .HouseNumber = "1"})
        db.Person.Insert(New With {.LastName = "lastname1", .FirstName = "firstname1", .AddressId = 1})
        db.Person.Insert(New With {.LastName = "lastname1", .FirstName = "firstname2", .AddressId = 1})
        db.BadHabit.Insert(New With {.BadHabit = "Drinks", .PersonId = 1})
        db.BadHabit.Insert(New With {.BadHabit = "Smokes", .PersonId = 1})
        db.BadHabit.Insert(New With {.BadHabit = "Eats to much", .PersonId = 2})
        ```
And now for the querying.

```vbnet
Dim result = db.Persons.FindAllByLastName("lastname1").WithAddress
        For Each person In result
            Console.WriteLine("{0} {1} {2} {3} {4}", person.Id, person.LastName, person.FirstName, person.Address.Street, person.Address.HouseNumber)
            For Each badHabit In person.BadHabits
                Console.WriteLine(badHabit.BadHabit)
            Next
        Next```
Dude, that&#8217;s simple&#8230; I guess that&#8217;s why they call it Simple.Data and not PetaPoco 😉

Of course the above still lazy loads and gives this SQL.

```tsql
Simple.Data.Ado: 
Text
select [Person].[Id],[Person].[LastName],[Person].[FirstName],[Person].[AddressId],[Address].[Id] AS [__with1__Address__Id],[Address].[Street] AS [__with1__Address__Street],[Address].[HouseNumber] AS [__with1__Address__HouseNumber] from [Person] LEFT JOIN [Address] ON ([Address].[Id] = [Person].[AddressId]) WHERE [Person].[LastName] = @p1
@p1 (String) = lastname1

Simple.Data.Ado: 
Text
select [BadHabit].[Id],[BadHabit].[BadHabit],[BadHabit].[PersonId] from [BadHabit] WHERE [BadHabit].[PersonId] = @p1
@p1 (Int32) = 1

Simple.Data.Ado: 
Text
select [BadHabit].[Id],[BadHabit].[BadHabit],[BadHabit].[PersonId] from [BadHabit] WHERE [BadHabit].[PersonId] = @p1
@p1 (Int32) = 2```
That again is not always a desired behaviour and in our case we just want to eagerly fetch our data. Which of course is stupidly simple to do just by adding the WithStatement like this.

```vbnet
Dim result = db.Persons.FindAllByLastName("lastname1").WithAddress.WithBadHabits
        For Each person In result
            Console.WriteLine("{0} {1} {2} {3} {4}", person.Id, person.LastName, person.FirstName, person.Address.Street, person.Address.HouseNumber)
            For Each BadHabit In person.BadHabits
                Console.WriteLine(BadHabit.BadHabit)
            Next
        Next```
And now I get this sql instead.

```vbnet
Simple.Data.Ado: 
Text
select [Person].[Id],[Person].[LastName],[Person].[FirstName],[Person].[AddressId],[Address].[Id] AS [__with1__Address__Id],[Address].[Street] AS [__with1__Address__Street],[Address].[HouseNumber] AS [__with1__Address__HouseNumber],[BadHabit].[Id] AS [__withn__BadHabits__Id],[BadHabit].[BadHabit] AS [__withn__BadHabits__BadHabit],[BadHabit].[PersonId] AS [__withn__BadHabits__PersonId] from [Person] LEFT JOIN [Address] ON ([Address].[Id] = [Person].[AddressId]) LEFT JOIN [BadHabit] ON ([Person].[Id] = [BadHabit].[PersonId]) WHERE [Person].[LastName] = @p1
@p1 (String) = lastname1```
And that&#8217;s a lot better.

You can however not do this.

```vbnet
person.BadHabits(0).BadHabit```
Because that will give you this error.

> No default member found for type &#8216;SimpleQuery&#8217;.

I think that is because BadHabits is of type SimpleQuery and the default for SimpleQuery is an IEnumerator. But you can do this instead.

```vbnet
person.BadHabits.ToList()(0).BadHabit```
## Conclusion

What can I say. It&#8217;s simple.