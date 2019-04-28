---
title: Trying out RavenDB with VB.Net
author: Christiaan Baes (chrissie1)
type: post
date: 2011-04-23T17:34:00+00:00
ID: 1137
excerpt: |
  Introduction
  
  If you don't know what a document database is you should go to Google and find out quick smart. I'm not saying document database are the future, but they are a part of it and they will suit some situations better than RDBMSes. I already&hellip;
url: /index.php/desktopdev/mstech/trying-out-ravendb-with-vb/
views:
  - 4262
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - ravendb
  - vb.net

---
## Introduction

If you don&#8217;t know what a document database is you should go to Google and find out quick smart. I&#8217;m not saying document database are the future, but they are a part of it and they will suit some situations better than RDBMSes. I already did some blogposts about MongoDB in the past.

  * [Time to try MongoDB][1]
  * [Trying out MongoDB persisting objects][2]
  * [MongoDB persisting System.Drawing.Image][3]

And now I&#8217;m going to try out [RavenDB][4] by [Ayende Rahien][5] and his minions. RavenDB is native .Net. 

## The start

You can use RavenDB in different form. As a service, as an ASP.Net service, embedded, &#8230; . I wanted first to try and run it embedded so I could run my unittests against it. So let&#8217;s if I can get it to work.

First I [downloaded the binaries][6], I unzipped them. Then I had to reference Raven.Client.Embedded.dll and the Raven.Client.Lightweight.dll that can be found in the embeddedclient folder. 

I made a small class I will use to test.

```vbnet
Namespace Model
    Public Class Plant
        Public Property Id As String
        Public Property LatinName As String
        Public Property EnglishNames As ISet(Of String)
        Public Property FlowerColor As String
    End Class
End Namespace```
The convention is to have a property Id as string which will be filled by RavenDB much like an identity field in SQL server. 

Then I made a little service class.

```vbnet
Imports Raven.Client

Namespace Services
    Public Class PlantService
        Private _session As IDocumentSession

        Public Sub New(ByVal session As IDocumentSession)
            _session = session
        End Sub

        Public Sub AddPLant(ByVal Plant As Model.Plant)
            _session.Store(Plant)
            _session.SaveChanges()
        End Sub

        Public Function GetPlant(ByVal Id As String) As Model.Plant
            Return _session.Load(Of Model.Plant)("plants/" & Id)
        End Function

        Public Function NumberOfPlants() As Integer
            Return _session.Query(Of Model.Plant)().Count()
        End Function
    End Class

End Namespace```
And a few tests to test this.

```vbnet
Imports Plants.Model
Imports NUnit.Framework
Imports Plants.Services

Namespace Tests
    &lt;TestFixture()&gt;
    Public Class TestPlantService

        &lt;Test()&gt;
        Public Sub CountNumberOfDocuments()
            Dim service = New PlantService(RavenDBSession.GetTestSession)
            service.AddPLant(New Plant With {.LatinName = "Fagus sylvatica"})
            service.AddPLant(New Plant With {.LatinName = "Quercus Robur"})
            Assert.AreEqual(2, service.NumberOfPlants)
        End Sub

        &lt;Test()&gt;
        Public Sub CanGetPlant()
            Dim service = New PlantService(RavenDBSession.GetTestSession)
            service.AddPLant(New Plant With {.LatinName = "Fagus sylvatica"})
            Dim plant = service.GetPlant("1")
            Assert.AreEqual("Fagus sylvatica", plant.LatinName)
        End Sub
    End Class
End Namespace```
And here is my little helper class that makes sessions.

```vbnet
Imports Raven.Client
Imports Raven.Client.Client
Imports Raven.Client.Document

Namespace Services

    Public Class RavenDBSession
        Private Shared _store As DocumentStore

        Public Shared Function GetNewSession() As IDocumentSession
            _store = New DocumentStore With {.Url = "http://localhost:8080"}
            _store.Initialize()
            Return _store.OpenSession
        End Function

        Public Shared Function GetTestSession() As IDocumentSession
            _store = New EmbeddableDocumentStore With {.RunInMemory = True}
            _store.Initialize()
            Return _store.OpenSession
        End Function
    End Class
End Namespace```
First thing to note if you have just created a new project in Visual studio 2010 is that by default they will be .Net framework 4.0 Client profile and the <span class="MT_red">EmbeddableDocumentStore does not work with the client profile</span> so you have to change that in your project. Right click your project and select properties, go to compile and then click the button Advanced compile options. Now change the Target framework to .Net Framework 4.0. 

As you can see above my live version will be working over http but more about that later.

The first time I ran these tests I got an error.

> TestPlantService.CanGetPlant : FailedSystem.IO.FileNotFoundException : Could not load file or assembly <span class="MT_red">&#8216;MissingBitsFromClientProfile, Version=0.0.0.0, Culture=neutral, PublicKeyToken=37f41c7f99471593&#8217;</span> or one of its dependencies. The system cannot find the file specified.
  
> at Raven.Client.Client.HttpJsonRequestFactory..ctor()
  
> at Raven.Client.Document.DocumentStore..ctor() in c:BuildsravenRaven.Client.LightweightDocumentDocumentStore.cs: line 47
  
> at Raven.Client.Client.EmbeddableDocumentStore..ctor() in c:BuildsravenRaven.Client.EmbeddedEmbeddableDocumentStore.cs: line 30
  
> at Plants.Services.RavenDBSession.GetTestSession() in RavenDBSession.vb: line 17
  
> at Plants.Tests.TestPlantService.CanGetPlant() in TestPlantService.vb: line 19 

So I added MissingBitsFromClientProfile.dll as a reference, I found it in then Client folder not the Embedded Client folder. And set Specific version to true to make sure it is copied to the output folder. And ran them again not forgetting to rebuild.

And now I get this error.

> TestPlantService.CanGetPlant : FailedSystem.InvalidOperationException : Could not find transactional storage type: Raven.Storage.Managed.TransactionalStorage, <span class="MT_red">Raven.Storage.Managed</span>
  
> at Raven.Database.Config.InMemoryRavenConfiguration.CreateTransactionalStorage(Action notifyAboutWork) in c:BuildsravenRaven.DatabaseConfigInMemoryRavenConfiguration.cs: line 272
  
> at Raven.Database.DocumentDatabase..ctor(InMemoryRavenConfiguration configuration) in c:BuildsravenRaven.DatabaseDocumentDatabase.cs: line 111
  
> at Raven.Client.Client.EmbeddableDocumentStore.InitializeInternal() in c:BuildsravenRaven.Client.EmbeddedEmbeddableDocumentStore.cs: line 130
  
> at Raven.Client.Document.DocumentStore.Initialize() in c:BuildsravenRaven.Client.LightweightDocumentDocumentStore.cs: line 400
  
> at Plants.Services.RavenDBSession.GetTestSession() in RavenDBSession.vb: line 18
  
> at Plants.Tests.TestPlantService.CanGetPlant() in TestPlantService.vb: line 19 

So I added the Raven.storage.Managed.dll also to the project as a reference and set Specific version to true to make sure it is copied to the output folder. And ran them again not forgetting to rebuild.

And now all is well. All tests turn green.

## Conclusion

All in all a very nice experience as soon as you know that it won&#8217;t run on the Client profile if you want to run it embedded. It&#8217;s a pity I can&#8217;t get better error messages from VS to tell me that this is the problem. And once I set the two missing dependencies it worked as expected. Hope this helps.

 [1]: /index.php/DesktopDev/MSTech/time-to-try-mongodb?highlight=mongodb&sentence=
 [2]: /index.php/DesktopDev/MSTech/trying-out-mongodb-persisting-objects?highlight=mongodb&sentence=
 [3]: /index.php/DesktopDev/MSTech/mongodb-persisting-system-drawing-image?highlight=mongodb&sentence=
 [4]: http://ravendb.net
 [5]: http://ayende.com/Blog/default.aspx
 [6]: http://builds.hibernatingrhinos.com/builds/ravendb