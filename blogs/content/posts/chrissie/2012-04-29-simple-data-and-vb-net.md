---
title: Simple.Data and VB.Net the beginning
author: Christiaan Baes (chrissie1)
type: post
date: 2012-04-29T06:20:00+00:00
ID: 1608
excerpt: Trying out the Simple.Data ORM from Mark Rendle with VB.Net
url: /index.php/desktopdev/mstech/simple-data-and-vb-net/
views:
  - 7177
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - simple.data
  - vb.net

---
## Introduction

After using [NHibernate][1] and [Massive][2] there was room for yet another ORM in my life. The ORM is called [Simple.Data][3]. Simple.Data was written by [Mark Rendle][4].

## Setting it up

I installed these packages.

  * Simple.Data.SqlCompact40 0.16.1.0
  * SqlServerCompact 4.0.8482.1

Because those were the packages that were available on Nuget.

So I&#8217;m gonna test Simple.Data with the SQLserver compact edition.

Simple.Data can be used with [many other databases][5] as well. And even some nosql databases.

To my surprise Simple.data also as a very good [Manual][3] available to you. But it only has C# samples. I&#8217;ll be using VB.Net for this post.

First I m gonna create a database.

```vbnet
Private Function CreateDatabase() As SqlCeEngine
        If File.Exists("test.sdf") Then File.Delete("test.sdf")
        Dim connectionString = "DataSource=""test.sdf""; Password=""mypassword"""
        Dim en = New SqlCeEngine(connectionString)
        en.CreateDatabase()
        Return en
    End Function```
The above will delete the database when it is already there and create a new one for me.

Then I have to add a table to my database.

```vbnet
Private Sub CreateTablePerson()
        Using cn = New SqlCeConnection("DataSource=""test.sdf""; Password=""mypassword""")
            If cn.State = ConnectionState.Closed Then
                cn.Open()
            End If
            Dim sql = "create table Person (LastName nvarchar (40) not null, FirstName nvarchar (40))"
            Using cmd = New SqlCeCommand(sql, cn)
                cmd.ExecuteNonQuery()
            End Using
        End Using
    End Sub```
This uses the same connectionstring and adds a very simple table called Person to my database.

Now I want to test the above. So I created a ConsoleApplication and in the Main function I add the calls to CreateDatabase and CreateTablePerson.

That was simple so far ;-).

The above was all SQL compact syntax and just to set us up with something to test Simple.Data with. Now on to the interesting part.

## Simple.Data

First we need to tell Simple.Data where it can find our database.

```vbnet
Dim db = Simple.Data.Database.OpenConnection("DataSource=""test.sdf""; Password=""mypassword""")
        ```
this gives us a database object which we will use to query our data.

For the next step you have to set <span class="MT_red">OPTION STRICT OFF</span> because Simple.Data uses the dynamic parts of the .Net framework.

First thing to do is to insert some data into our table. 

```vbnet
db.Persons.Insert(New With {.LastName = "lastname1", .FirstName = "firstname1"})
        ```
We use the db object to tell it to insert something into the table Persons. Notice how our table is called Person in the database but we can use Persons as our identifier for Simple.Data? Yeah that is awesome. But you can also write this.

```vbnet
db.Person.Insert(New With {.LastName = "lastname1", .FirstName = "firstname1"})
        ```
And Simple.Data will still figure it out. 

Of course a good set of integration tests is required to make sure that Simple.Data does the right thing all the time would be wise choise because we are relying on a bit of magic here to do our work for us, and magic does not always do what we expect it to do.

Now that we have our data in the table we can query it and write it out to the console.

```vbnet
Dim result = db.Persons.FindByLastName("lastname1")
        Console.WriteLine(result.LastName & " " & result.FirstName)
        ```
So again we use the Person table and now we use the FindBy qualifier followed by the name of our column and then we just pass our value to that, very simple.

Go read [this part of the documentation][6] to find out how it works. 

In essence I&#8217;m also countin on it returning just one object. But what happens if multiple objects come back.

First I make sure to insert 2 rows with the same lastname.

```vbnet
db.Person.Insert(New With {.LastName = "lastname1", .FirstName = "firstname1"})
        db.Person.Insert(New With {.LastName = "lastname1", .FirstName = "firstname2"})
        ```
And then I do this again.

```vbnet
Dim result = db.Persons.FindByLastName("lastname1")
        Console.WriteLine(result.LastName & " " & result.FirstName)
        ```
and that will just return the first one. 

If I want to get all persons with that lastname I have to use FindAllByLastName like so.

```vbnet
Dim result = db.Persons.FindAllByLastName("lastname1")
        ```
Of course now I will have to change my Console.WriteLine too.

```vbnet
Dim result = db.Persons.FindAllByLastName("lastname1")
        For Each person In result
            Console.WriteLine(person.LastName & " " & person.FirstName)
        Next```
## Conclusion

Simple.Data is pretty cool and it makes querying your database very simple and neat. But since this is all done by using the Dynamic Language Runtime I would hihgly recommend having Automated tests.

 [1]: http://nhforge.org/Default.aspx
 [2]: https://github.com/robconery/massive
 [3]: https://github.com/markrendle/Simple.Data
 [4]: http://blog.markrendle.net/
 [5]: http://simplefx.org/simpledata/docs/#/content%2F01%20Getting%20Started%2F02%20Supported%20Databases.html
 [6]: http://simplefx.org/simpledata/docs/#/content%2F02%20Basics%2F00%20Naming%20Conventions.html