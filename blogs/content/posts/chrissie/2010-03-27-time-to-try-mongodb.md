---
title: Time to try MongoDB
author: Christiaan Baes (chrissie1)
type: post
date: 2010-03-27T05:47:39+00:00
ID: 739
excerpt: "My first little try with MongoDB a NoSQL database which also has a driver for .net and therefor I can and will use it with VB.Net. I'll show you how I set it up and how it works."
url: /index.php/desktopdev/mstech/time-to-try-mongodb/
views:
  - 9038
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - mongodb
  - vb.net

---
I guess this NoSQL thing is getting to me. So I had to try it and see if I can use it with my favorite language.

I first did a little research on the net and found [this post][1].

I went on from there.

First I [downloaded][2] [MongoDB][3].

Then you unzip it and go to the bin folder and run mongod.exe. By carefull with firewall issues.

Now we create a folder named c:datadb

then I downloaded [a driver for .net][4].

I then referenced the three dll&#8217;s. Just to be sure ;-).

> MongoDB.Driver.dll
  
> MongoDB.GridFS.dll
  
> MongoDB.linq.dll

So far no problems. Now lets create a console application and see how easy it is to set up.

```vb.net
Imports MongoDB.Driver

Module Module1

    Sub Main()
        Dim mongo As New Mongo
        mongo.Connect()
        Dim db = mongo.GetDatabase("person")
        Dim persons = db.GetCollection("persons")
        Console.WriteLine("You have " & persons.Count & " persons in your database")
        
        Console.ReadLine()
    End Sub

End Module
```
If you run this you should get

> You have 0 persons in your database

Now lets add a person to our database.

```vbnet
Dim person As New Document
        person("name") = "name1"
        person("firstname") = "firstname2"
        persons.Insert(person)
        Console.WriteLine("You have " & persons.Count & " persons in your database")
```
The above code should now give you the following result.

> You have 0 persons in your database
  
> You have 1 persons in your database

Cool.

But the second time you run it you get this.

> You have 1 persons in your database
  
> You have 2 persons in your database

And the third

> You have 2 persons in your database
  
> You have 3 persons in your database

And so on&#8230;

So time to delete some stuff.

```vbnet
persons.Delete(New Document)
        Console.WriteLine("You have " & persons.Count & " persons in your database")```
see the delete with the New Document parameter? That deletes all documents from the database. 

So the result will now be.

> You have 3 persons in your database
  
> You have 4 persons in your database
  
> You have 0 persons in your database

Easy.

Now lets look for a certain record.

```vbnet
Dim mongo As New Mongo
        mongo.Connect()
        Dim db = mongo.GetDatabase("person")
        Dim persons = db.GetCollection("persons")
        Console.WriteLine("You have " & persons.Count & " persons in your database")
        Dim person As New Document
        person("name") = "name1"
        person("firstname") = "firstname1"
        persons.Insert(person)
        Dim person2 As New Document
        person2("name") = "name2"
        person2("firstname") = "firstname2"
        persons.Insert(person2)
        Console.WriteLine("You have " & persons.Count & " persons in your database")
        Dim persontofind = New Document
        persontofind("name") = "name1"
        Dim personfound = persons.FindOne(persontofind)
        Console.WriteLine("Found person: " & personfound("name") & " " & personfound("firstname"))
        persons.Delete(New Document)
        Console.WriteLine("You have " & persons.Count & " persons in your database")
        Console.ReadLine()```
Now the result of the above will be.

> You have 0 persons in your database
  
> You have 1 persons in your database
  
> Found person: name1 firstname1
  
> You have 0 persons in your database

All very simple untill now. Only took me a couple of minutes to figure out. 

More on this later.

 [1]: http://odetocode.com/Blogs/scott/archive/2009/10/13/experimenting-with-mongodb-from-c.aspx
 [2]: http://www.mongodb.org/display/DOCS/Downloads
 [3]: http://www.mongodb.org/
 [4]: http://github.com/samus/mongodb-csharp/downloads