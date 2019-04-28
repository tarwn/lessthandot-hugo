---
title: Trying out MongoDB persisting objects
author: Christiaan Baes (chrissie1)
type: post
date: 2010-03-28T07:05:38+00:00
ID: 742
url: /index.php/desktopdev/mstech/trying-out-mongodb-persisting-objects/
views:
  - 4595
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - mongodb
  - vb.net

---
This is the second part in my attempt to learn MongDB.

[Part 1.][1] 

As you could see in part 1, MongoDB is very magic stringie. And I don&#8217;t like magic stringies. But not to worry, Google to the rescue and a certain Mohammad Azam ([azamsharp][2]). 

I found this C# [code][3] and with his permission I translated it to VB.Net.

He made a bunch of extension methods to easliy serialize from a MongoDB Document to your object and the other way around.

```vbnet
Imports MongoDB.Driver
Imports System.Web.Script.Serialization
Imports System.Runtime.CompilerServices
Imports System.Reflection

Module Module2
    &lt;Extension()&gt; _
    Public Function ToOid(ByVal id As String) As Oid
        If id.Length = 24 Then
            Return New Oid(id)
        End If
        Return New Oid(id.Replace("""", ""))
    End Function

    &lt;Extension()&gt; _
    Public Function ToIEnumerable(Of T)(ByVal documents As IEnumerable(Of Document)) As IEnumerable(Of T)
        Dim list = New List(Of T)()
        For Each document In documents
            list.Add(document.ToClass(Of T))
        Next
        Return list
    End Function

    &lt;Extension()&gt; _
    Public Function ToClass(Of T)(ByVal source As Document) As T
        Return New JavaScriptSerializer().Deserialize(Of T)(source.ToString())
    End Function

    &lt;Extension()&gt; _
    Public Function ToDocument(ByVal source As Object) As Document
        Dim document = SerializeMember(source)
        Dim _property = document.GetType().GetProperty("Document")
        If _property IsNot Nothing Then
            _property.SetValue(source, document, Nothing)
        End If
        Return document
    End Function

    &lt;Extension()&gt; _
    Private Function SerializeMember(ByVal source As Object) As Object
        If (Not Type.GetTypeCode(source.GetType()).Equals(TypeCode.Object)) Then
            Return source
        End If

        If TypeOf source Is Byte() Then
            Return source
        End If

        If TypeOf source Is IEnumerable Then

            Dim documents = New List(Of Object)

            For Each doc In source
                documents.Add(SerializeMember(doc))
            Next
            Return documents.ToArray()
        End If

        Dim properties = source.GetType().GetProperties(BindingFlags.Instance AndAlso BindingFlags.Public)

        Dim Document = New Document()

        For Each _property In properties
            Dim propertyValue = _property.GetValue(source, Nothing)
            If (propertyValue Is Nothing) Then Continue For

            If _property.Name.Equals("_id") Then
                Document.Add(_property.Name, ((propertyValue.ToString).ToOid()))
            Else
                Document.Add(_property.Name, SerializeMember(propertyValue))
            End If
        Next
        Return Document
    End Function

End Module
```
Now I can easily change my code to this.

First a class person.

```vbnet
Public Class Person
        Private _Name As String
        Private _FirstName As String

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
        End Property```
And now some code to prove how it works.

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
So now instead of this.

```vbnet
Dim person As New Document
        person("name") = "name1"
        person("firstname") = "firstname2"
        persons.Insert(person)
```
I can do this

```vbnet
Dim person As New Person
        person.Name = "name1"
        person.FirstName = "firstname1"
        persons.Insert(person.ToDocument)
        ```
a lot less magic strings.

And instead of this

```vbnet
For Each personfound In persons.FindAll.Documents
            Console.WriteLine("Found person: " & personfound("_id").ToString & " " & personfound("Name") & " " & personfound("FirstName"))
        Next```
We now do this.

```vbnet
For Each personfound In persons.FindAll.Documents
            Dim person1 = personfound.ToClass(Of Person)()
            Console.WriteLine("Found person: " & person1.Name & " " & person1.FirstName)
        Next```
Azamsharp also made a nice [screencast][4] to demonstrate MongoDB.

 [1]: /index.php/DesktopDev/MSTech/time-to-try-mongodb
 [2]: http://www.azamsharp.com/
 [3]: http://highoncoding.com/Articles/686_Implementing_Blog_Using_ASP_NET_MVC_and_MongoDb.aspx
 [4]: http://highoncoding.com/Articles/685_Persisting_Objects_in_the_MongoDb_Database_Using_C__Driver.aspx