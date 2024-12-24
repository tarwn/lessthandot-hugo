---
title: Trying out RavenHQ with VB.Net
author: Christiaan Baes (chrissie1)
type: post
date: 2012-05-24T09:51:00+00:00
ID: 1637
excerpt: RavenHQ is a cloudy solution for RavenDB.
url: /index.php/desktopdev/mstech/trying-out-ravenhq/
views:
  - 8487
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - ravendb
  - ravenhq

---
[RavenHQ][1] is a cloudy solution for [RavenDB][2]. And it has a free option to try it out. So I did.

First you create an account and then you add a database.

To make a database you just fill in the database name and and click on the red add button for the bronze plan.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ravenhq/RavenHQ1.png?mtime=1337852093"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ravenhq/RavenHQ1.png?mtime=1337852093" width="987" height="556" /></a>
</div>

And when you now go to databases you will see this.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ravenhq/RavenHQ2.png?mtime=1337852213"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ravenhq/RavenHQ2.png?mtime=1337852213" width="963" height="290" /></a>
</div>

Before we go on with our coding we need to know our connection details. These can be found when you click on the manage button for your database and then connectionstrings.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ravenhq/RavenHQ3.png?mtime=1337859156"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ravenhq/RavenHQ3.png?mtime=1337859156" width="960" height="440" /></a>
</div>

Now that we got this we just need our Model.

```vbnet
Public Class Dvd
        Public Property Title As String
    End Class```
First thing to then do is, is to make a connection.

Which we do like this.

```vbnet
Dim documentStore = New DocumentStore() With {.Url = "https://1.ravenhq.com/databases/chrissie1-DVD", .ApiKey = "theapikeygoeshere"}
        documentStore.Initialize()
        ```
And then we can save a document to the database.

```vbnet
Dim session = documentStore.OpenSession()
        session.Store(New Dvd With {.Title = "Star Trek 11"})
        session.SaveChanges()
        ```
Once you have a few document in the database you can query them.

Here I&#8217;m gonna do a bad thing and show them all on the screen.

```vbnet
Dim dvds = session.Query(Of Dvd)()
        Console.WriteLine("Loading Dvds")
        For Each Dvd In dvds
            Console.WriteLine(session.Advanced.GetDocumentId(Dvd))
            Console.WriteLine(Dvd.Title)
        Next
        Console.WriteLine("Dvds loaded")
        Console.ReadLine()```
As you can see my model does not have an id but RavenDb still made one for me. I could have added and Id property to my model and then RavenDb would have used that.

You can also see those documents in ravenHQ by going to the Management studio, the url for that can be found under the database info section.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ravenhq/RavenHQ4.png?mtime=1337859990"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ravenhq/RavenHQ4.png?mtime=1337859990" width="961" height="566" /></a>
</div>

The management studio will then let you view, edit, add and delete documents and much more.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ravenhq/RavenHQ5.png?mtime=1337860203"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/ravenhq/RavenHQ5.png?mtime=1337860203" width="1387" height="870" /></a>
</div>

Go check it out and have fun.

 [1]: https://ravenhq.com/
 [2]: http://ravendb.net/