---
title: MongoDB persisting System.Drawing.Image
author: Christiaan Baes (chrissie1)
type: post
date: 2010-03-29T05:20:14+00:00
ID: 743
url: /index.php/desktopdev/mstech/mongodb-persisting-system-drawing-image/
views:
  - 7824
categories:
  - Microsoft Technologies
tags:
  - mongodb
  - vb.net

---
Allthough the [MongoDB documentation][1] says that it support binary data, I don&#8217;t think the .Net driver really does. 

>   * Document-oriented storage (the power and flexibility of JSON-like data schemas)
>   * Dynamic queries
>   * Full index support, including secondary indexes, inner-objects, embedded arrays, geospatial
>   * Query profiling
>   * Fast, in-place updates
>   * Efficient storage of binary data large objects (e.g. photos and videos)
>   * Replication and fail-over support
>   * Auto-sharding for cloud-level scalability
>   * MapReduce for complex aggregation
>   * Commercial Support, Training, and Consulting

Beacuse when I try to add an image as property the whole thing blows up.

But we could just treat it as json and be done with it.
  
json is pretty limited to these things.

> JSON&#8217;s basic types are:
> 
>   * Number (integer or real)
>   * String (double-quoted Unicode with backslash escaping)
>   * Boolean (true or false)
>   * Array (an ordered sequence of values, comma-separated and enclosed in square brackets)
>   * Object (a collection of key:value pairs, comma-separated and enclosed in curly braces)
>   * null

That would mean that we encode our image as Base64 string and then read it back in.

So I changed my Person class to this.

```vbnet
Public Class Person
        Private _Name As String
        Private _FirstName As String
        Private _Image As System.Drawing.Bitmap

        Public Sub New()
            _Name = ""
        End Sub

        Public Property Name() As String
            Get
                Return _Name
            End Get
            Set(ByVal value As String)
                _Name = value
            End Set
        End Property

        Public Property FirstName() As String
            Get
                Return _FirstName
            End Get
            Set(ByVal value As String)
                _FirstName = value
            End Set
        End Property

        Public Function GetImage() As System.Drawing.Image
            Return _Image
        End Function

        Public Sub SetImage(ByVal Image As System.Drawing.Image)
            _Image = Image
        End Sub

        Public Property Image() As String
            Get
                Dim base64string As String = ""
                Try
                    Dim ms = New System.IO.MemoryStream()
                    _Image.Save(ms, System.Drawing.Imaging.ImageFormat.Jpeg)
                    Dim imageBytes = ms.ToArray()
                    base64string = Convert.ToBase64String(imageBytes)
                Catch ex As Exception

                End Try
                Return base64String
            End Get
            Set(ByVal value As String)
                Try
                    Dim imageBytes = Convert.FromBase64String(value)
                    Dim ms = New System.IO.MemoryStream(imageBytes, 0, imageBytes.Length)
                    ms.Write(imageBytes, 0, imageBytes.Length)
                    _Image = System.Drawing.Image.FromStream(ms, True)
                Catch ex As Exception
                    _Image = Nothing
                End Try
           End Set
        End Property
    End Class```
And now my code just works.

```vbnet
Sub Main()
        Dim mongo As New Mongo
        mongo.Connect()
        Dim db = mongo.GetDatabase("person")
        Dim persons = db.GetCollection("persons")
        Console.WriteLine("You have " & persons.Count & " persons in your database")
        Dim person As New Person
        person.Name = "name1"
        person.FirstName = "firstname1"
        person.SetImage(System.Drawing.Image.FromFile("C:UserschristiaanPicturesBackgroundskoala.jpg"))
        persons.Insert(person.ToDocument)
        Dim person2 As New Person
        person2.Name = "name2"
        person2.FirstName = "firstname2"
        persons.Insert(person2.ToDocument)
        Console.WriteLine("You have " & persons.Count & " persons in your database")
        For Each personfound In persons.FindAll.Documents
            Console.WriteLine("Found person: " & personfound("_id").ToString & " " & personfound("Name") & " " & personfound("FirstName"))
        Next
        For Each personfound In persons.FindAll.Documents
            Dim person1 = personfound.ToClass(Of Person)()
            Console.WriteLine("Found person: " & person1.Name & " " & person1.FirstName)
        Next
        persons.Delete(New Document)
        Console.WriteLine("You have " & persons.Count & " persons in your database")
        Console.ReadLine()
    End Sub```
But of course that isn&#8217;t showing us the Image is actualy usable.

So I did this instead.

```vbnet
Imports MongoDB.Driver

Public Class Form1

    Private Sub Form1_Load(ByVal sender As System.Object, ByVal e As System.EventArgs) Handles MyBase.Load
        Dim mongo As New Mongo
        mongo.Connect()
        Dim db = mongo.GetDatabase("person")
        Dim persons = db.GetCollection("persons")
        Dim person As New Person
        person.Name = "name1"
        person.FirstName = "firstname1"
        person.SetImage(System.Drawing.Image.FromFile("C:UserschristiaanPicturesBackgroundskoala.jpg"))
        persons.Insert(person.ToDocument)
        For Each personfound In persons.FindAll.Documents
            Dim person1 = personfound.ToClass(Of Person)()
            PictureBox1.Image = person1.GetImage
        Next
        persons.Delete(New Document)
    End Sub
End Class```
And hey presto.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/koala.png" alt="" title="" width="302" height="302" />
</div>

See also

  * [Part 1][2]
  * [Part 2][3]

> <span class="MT_red">I would not recommend saving large amounts of images in any database. Keep them in the filesystem and save the path to it.</span>

 [1]: http://www.mongodb.org/display/DOCS/Home
 [2]: /index.php/DesktopDev/MSTech/time-to-try-mongodb
 [3]: /index.php/DesktopDev/MSTech/trying-out-mongodb-persisting-objects