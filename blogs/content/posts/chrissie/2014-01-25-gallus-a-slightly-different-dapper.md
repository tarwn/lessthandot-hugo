---
title: Gallus, a slightly different dapper
author: Christiaan Baes (chrissie1)
type: post
date: 2014-01-25T10:17:43+00:00
ID: 2322
excerpt: Looking at Gallus which is supposed to be a slightly different than dapper.
url: /index.php/desktopdev/mstech/vbnet/gallus-a-slightly-different-dapper/
views:
  - 6316
categories:
  - VB.NET
tags:
  - Dapper
  - Gallus
  - vb.net

---
A certain <a href="https://twitter.com/d_mcsorley" title="David McSorley on twitter" target="_blank">David McSorley</a> on twitter told me had written a better dapper. One that could load collections which is something dapper doesn&#8217;t do.

The project is called <a href="https://github.com/d-mcsorley/Gallus" title="Gallus on github" target="_blank">Gallus and is on github.</a> But not yet on nuget. 

So I set out and tried it. 

Being that it is not on nuget and thus it has no compiled version of it and I work with VB.Net I had to create a second project, namely a C# class library. And then I just copy-pasted the code in this <a href="https://github.com/d-mcsorley/Gallus/blob/master/Gallus/Gallus.cs" title="Gallus.cs class." target="_blank">class </a>to that project. And I referenced that project in my VB.Net project. So now that we got it setup we can start playing around with it.

Since the difference with dapper is rather small I just tried the collection mapping for now.

Here is a piece of code I used to setup the database, which is sqlserver.

```vbnet
Imports Dapper
Imports System.Data.SqlClient

Public Module SetupDatabase

    Function Setup(withTestData As Boolean) As IDbConnection
        Using db1 = New SqlConnection("Data Source=localhost;Initial Catalog=master;Integrated Security=True")
            db1.Open()
            CreateDatabase(db1)
        End Using
        Dim db = New SqlConnection("Data Source=localhost;Initial Catalog=test;Integrated Security=True")
        db.Open()
        CreateTables(db)
        If withTestData Then InsertTestData(db)
        Return db
    End Function

    Private Sub InsertTestData(ByVal db As IDbConnection)
        Using tran = db.BeginTransaction
            db.Execute("Insert into address (street, housenumber) values (@street,@housenumber)", New With {.street = "street1", .housenumber = "1"}, tran)
            db.Execute("Insert into address (street, housenumber) values (@street,@housenumber)", New With {.street = "street2", .housenumber = "2"}, tran)
            db.Execute("Insert into person (lastname, firstname, addressid) values (@lastname,@firstname,@addressid)", New With {.lastname = "lastname1", .firstname = "firstname1", .addressid = 1}, tran)
            db.Execute("Insert into person (lastname, firstname, addressid) values (@lastname,@firstname,@addressid)", New With {.lastname = "lastname1", .firstname = "firstname2", .addressid = 1}, tran)
            db.Execute("Insert into person (lastname, firstname, addressid) values (@lastname,@firstname,@addressid)", New With {.lastname = "lastname2", .firstname = "firstname3", .addressid = 2}, tran)
            db.Execute("Insert into BadHabit (BadHabit) values (@BadHabit)", New With {.BadHabit = "Drinks"}, transaction:=tran)
            db.Execute("Insert into BadHabit (BadHabit) values (@BadHabit)", New With {.BadHabit = "Smokes"}, transaction:=tran)
            db.Execute("Insert into BadHabit (BadHabit) values (@BadHabit)", New With {.BadHabit = "Eats to much"}, transaction:=tran)
            db.Execute("Insert into BadHabitPerson (BadHabitId, PersonId) values (@BadHabitid, @PersonId)", New With {.BadHabitId = 1, .PersonId = 1}, tran)
            db.Execute("Insert into BadHabitPerson (BadHabitId, PersonId) values (@BadHabitid, @PersonId)", New With {.BadHabitId = 2, .PersonId = 1}, tran)
            db.Execute("Insert into BadHabitPerson (BadHabitId, PersonId) values (@BadHabitid, @PersonId)", New With {.BadHabitId = 3, .PersonId = 2}, tran)
            db.Execute("Insert into BadHabitPerson (BadHabitId, PersonId) values (@BadHabitid, @PersonId)", New With {.BadHabitId = 3, .PersonId = 1}, tran)
            db.Execute("Insert into BadHabitPerson (BadHabitId, PersonId) values (@BadHabitid, @PersonId)", New With {.BadHabitId = 3, .PersonId = 3}, tran)
            tran.Commit()
        End Using
    End Sub

    Private Sub CreateDatabase(db As IDbConnection)
        db.Execute("IF EXISTS(select * from sys.databases where name='test') DROP DATABASE test")
        db.Execute("CREATE DATABASE test")
    End Sub

    Private Sub CreateTables(db As IDbConnection)
        Using tran = db.BeginTransaction
            db.Execute("CREATE TABLE Address (Id int IDENTITY(1,1) PRIMARY KEY, Street nvarchar (40) NOT NULL, HouseNumber nvarchar (10))", transaction:=tran)
            db.Execute("CREATE TABLE Person (Id int IDENTITY(1,1) PRIMARY KEY, LastName nvarchar (40) NOT NULL, FirstName nvarchar (40), AddressId int NOT NULL)", transaction:=tran)
            db.Execute("ALTER TABLE Person ADD CONSTRAINT FK_Person_Address FOREIGN KEY (AddressId) REFERENCES Address(Id)", transaction:=tran)
            db.Execute("CREATE TABLE BadHabit (Id int IDENTITY(1,1) PRIMARY KEY, BadHabit nvarchar (40) NOT NULL)", transaction:=tran)
            db.Execute("CREATE TABLE BadHabitPerson (BadHabitId int NOT NULL, PersonId int NOT NULL)", transaction:=tran)
            db.Execute("ALTER TABLE BadHabitPerson ADD PRIMARY KEY(BadHabitId, PersonId)", transaction:=tran)
            db.Execute("ALTER TABLE BadHabitPerson ADD CONSTRAINT FK_BadHabitPerson_Person FOREIGN KEY (PersonId) REFERENCES Person(Id)", transaction:=tran)
            db.Execute("ALTER TABLE BadHabitPerson ADD CONSTRAINT FK_BadHabitPerson_BadHabit FOREIGN KEY (BadHabitId) REFERENCES BadHabit(Id)", transaction:=tran)
            tran.Commit()
        End Using
    End Sub

End Module
```
The above is there because I like you all, remember that.

And here is the model.

```vbnet
Public Class Person
        Public Property LastName As String
        Public Property FirstName As String
        Public Property Address As Address
        Public Property BadHabits As IList(Of BadHabit)
    End Class

    Public Class BadHabit
        Public BadHabit As String
    End Class

    Public Class Address
        Public Property Street As String
        Public Property HouseNumber As String
    End Class
```
And now for the interesting part.

This is how I would fill those objects in dapper.

```vbnet
Imports Dapper

Module Module1

    Sub Main()
        Dim db = SetupDatabase.Setup(True)
        Dim sql1 = "select * from person p left join address a on p.addressid = a.id where p.id = @personid" & ControlChars.CrLf
        sql1 &= "select * from badhabit b join badhabitperson bp on b.id = bp.badhabitid where bp.personid = @personid"
        Dim multi = db.QueryMultiple(sql1, New With {.personid = 1})
        Dim person = multi.Read(Of Person, Address, Person)(Function(p, a)
                                                                p.Address = a
                                                                Return p
                                                            End Function).SingleOrDefault
        person.BadHabits = multi.Read(Of BadHabit).ToList
        Console.WriteLine(Person.LastName)
        Console.WriteLine(Person.FirstName)
        Console.WriteLine(Person.Address.Street)
        Console.WriteLine(Person.Address.HouseNumber)
        Console.WriteLine(Person.BadHabits.Count)
        Console.ReadLine()
    End Sub
End Module
```
You should notice I am using the QueryMultiple and the mapping for address and then the second read to get the list of badhabits. That is one transaction and one connection to the database. Not counting the setup of the testdata of course.

Let me cut that down for you to make it less cluttered.

```vbnet
Dim sql1 = "select * from person p left join address a on p.addressid = a.id where p.id = @personid" & ControlChars.CrLf
        sql1 &= "select * from badhabit b join badhabitperson bp on b.id = bp.badhabitid where bp.personid = @personid"
        Dim multi = db.QueryMultiple(sql1, New With {.personid = 1})
        Dim person = multi.Read(Of Person, Address, Person)(Function(p, a)
                                                                p.Address = a
                                                                Return p
                                                            End Function).SingleOrDefault
        person.BadHabits = multi.Read(Of BadHabit).ToList
```
Now let&#8217;s compare that with Gallus.

```vbnet
Dim sql2 = "select * from person p left join address a on p.addressid = a.id join badhabitperson bp on p.id = bp.personid join badhabit b on b.id = bp.badhabitid where p.id = @personid"
        Dim person = db.Query(Of Person, Address, IList(Of BadHabit))(sql3, Sub(p, a, b)
                                                                                p.Address = a
                                                                                p.BadHabits = b
                                                                            End Sub, New With {.personid = 1}).SingleOrDefault
```
That&#8217;s a lot shorter. Just one query not 2. And the lambda is an action so it needs one less generic parameter than dapper needs.

So maybe something to look out for if that is one of your problems with dapper.

Is this enough reason for me to switch? Not just yet, since that is not one of the features I use in dapper on a daily basis.