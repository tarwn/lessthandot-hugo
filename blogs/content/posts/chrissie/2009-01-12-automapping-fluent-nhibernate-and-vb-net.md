---
title: automapping fluent nhibernate and VB.Net
author: Christiaan Baes (chrissie1)
type: post
date: 2009-01-12T09:30:36+00:00
ID: 281
url: /index.php/architect/designingsoftware/automapping-fluent-nhibernate-and-vb-net/
views:
  - 11605
categories:
  - Designing Software
tags:
  - fluentnhibernate
  - nhibernate
  - vb.net

---
I thought it was time to use fluent nhibernate just to keep up. Up untill now I still use the old atributes and nhibernate 1.2.1 because I see no reason to upgrade. It all just works and does what it is supposed to do. 

But I don&#8217;t want to stand in the way of progress and I want to help the VB.Net community ahead. So that [Jeremy][1] can feel a bit better about the VB.Net community ;-). 

First thing I did was checkout the trunk versions of [nHibernate][2] and [fluentnhibernate][3] 

Then I made a huge database with one table in it. I used SQL server 2005 64 bits (just in case Ted is reading this) for this. 

this is the one table magic.

```tsql
USE [fluentnhibernate]
GO
/****** Object:  Table [dbo].[Person]    Script Date: 01/12/2009 10:54:14 ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[Person](
	[Id] [uniqueidentifier] NOT NULL CONSTRAINT [DF_Person_Id]  DEFAULT (newid()),
	[LastName] [nvarchar](50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
	[FirstName] [nvarchar](50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL
) ON [PRIMARY]```
And I filled up the table with this.

```tsql
INSERT INTO [fluentnhibernate].[dbo].[Person]
           ([LastName]
           ,[FirstName])
     VALUES
           ('Baes'
           ,'Christiaan')

INSERT INTO [fluentnhibernate].[dbo].[Person]
           ([LastName]
           ,[FirstName])
     VALUES
           ('Gobo'
           ,'Denis')```
So the corresponding model class should be simple enough too.

```vbnet
Namespace Model
    Public Class Person
        Private _id As Guid
        Private _lastName As String
        Private _firstName As String

        Public Sub New()
            _id = Guid.NewGuid
            _lastName = ""
            _firstName = ""
        End Sub

        Public Overridable Property Id() As Guid
            Get
                Return _id
            End Get
            Set(ByVal value As Guid)
                _id = value
            End Set
        End Property

        Public Overridable Property LastName() As String
            Get
                Return _lastName
            End Get
            Set(ByVal value As String)
                _lastName = value
            End Set
        End Property

        Public Overridable Property FirstName() As String
            Get
                Return _firstName
            End Get
            Set(ByVal value As String)
                _firstName = value
            End Set
        End Property

    End Class
End Namespace```
First of all I added the fluentnhibernate project to my new solution, quickly noticing that it was not wise to call my testproject fluentnhibernate and even quicker changing that to fluentnhibernatetest.

I also added nhibernate to the solution.

And I added them as a reference to the testproject.

And then I did this.

```vbnet
Option Infer On

Imports FluentNHibernate.AutoMap
Imports fluentnhibernatetest.Model
Imports FluentNHibernate

Namespace Dal
    Public Class nHibernateConfiguration
        Public Shared SessionFactory As NHibernate.ISessionFactory
        Public Shared ConnectionString As String = "Server=servername;Database=fluentnhibernate;Trusted_Connection=True;"

        Public Shared Function confignhibernate() As NHibernate.ISessionFactory
            Dim properties = New Dictionary(Of String, String)
            properties.Add("connection.connection_string", ConnectionString)
            properties.Add("connection.provider", "NHibernate.Connection.DriverConnectionProvider")
            properties.Add("dialect", "NHibernate.Dialect.MsSql2005Dialect")
            properties.Add("connection.driver_class", "NHibernate.Driver.SqlClientDriver")
            properties.Add("show_sql", "true")
            properties.Add("proxyfactory.factory_class", "NHibernate.ByteCode.LinFu.ProxyFactoryFactory, NHibernate.ByteCode.LinFu")

            Dim autoMappings = AutoPersistenceModel _
                                    .MapEntitiesFromAssemblyOf(Of Person) _
                                    .Where(Function(t) t.Namespace = "fluentnhibernatetest.Model")

            SessionFactory = New NHibernate.Cfg.Configuration() _
                                            .AddProperties(properties) _
                                            .AddAutoMappings(autoMappings) _
                                            .BuildSessionFactory()
            Return SessionFactory
        End Function
    End Class
End Namespace```
which meant I also had to import and reference the NHibernate.ByteCode.Linfu project/assembly. Cant&#8217;t remember ever needing that.

And of course I also neede something to run this and see if it worked. Of course I could have created a testproject but what&#8217;s the fun in that.

```vbnet
Option Infer On

Imports fluentnhibernatetest.Model
Imports NHibernate

Module Module1

    Sub Main()
        Try
            Dim sessionfactory = Dal.nHibernateConfiguration.confignhibernate
            Dim session As ISession = sessionfactory.OpenSession()
            Dim _ReturnList = session.CreateCriteria(GetType(Model.Person)).List(Of Model.Person)()
            For Each Person In _ReturnList
                Console.WriteLine(Person.Id.ToString & " " & Person.LastName & " " & Person.FirstName)
            Next
        Catch ex As Exception
            Console.WriteLine(ex.Message)
            Console.WriteLine(ex.StackTrace)
        End Try
        Console.ReadLine()
    End Sub

End Module
```
With this as the result of all our hard labor.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/Architect/fluentnhibernateresult.jpg" alt="" title="" width="668" height="335" />
</div>

Not that difficult ;-). 

I will do a more elaborate test later on.

Once again thanks to the guys that sacrificied their time and energy in making this happen.

you can also find more examples in C# [here][4] by [James Gregory][5].

 [1]: http://codebetter.com/blogs/jeremy.miller/archive/2009/01/07/a-challenge-to-the-vb-net-community-at-large-on-oss.aspx
 [2]: https://nhibernate.svn.sourceforge.net/svnroot/nhibernate/trunk/nhibernate
 [3]: http://fluent-nhibernate.googlecode.com/svn/trunk
 [4]: http://blog.jagregory.com/2009/01/11/fluent-nhibernate-auto-mapping-entity-conventions/
 [5]: http://blog.jagregory.com/