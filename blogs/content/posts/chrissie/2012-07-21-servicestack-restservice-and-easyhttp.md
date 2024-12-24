---
title: servicestack, restservice and easyhttp
author: Christiaan Baes (chrissie1)
type: post
date: 2012-07-21T15:43:00+00:00
ID: 1677
excerpt: |
  A long time ago (last week) I wrote about servicestack and easyhttp.
  
  now I would like to talk to you about restservice. Restservice is a way to make a restfull service with servicestack.
  
  First let's build our service.
  
  Imports ServiceStack.Servi&hellip;
url: /index.php/webdev/serverprogramming/servicestack-restservice-and-easyhttp/
views:
  - 14458
categories:
  - Server Programming
tags:
  - easyhttp
  - servicestack
  - vb.net

---
## Introduction

A long time ago (last week) I wrote about [servicestack and easyhttp][1].

now I would like to talk to you about restservice. Restservice is a way to make a restfull service with servicestack.

## The service

First let&#8217;s build our service.

```vbnet
Imports ServiceStack.ServiceInterface
Imports ServiceStackModel.Response
Imports ServiceStackModel.Request
Imports ServiceStack.ServiceHost

Public Class PlantService
    Inherits RestServiceBase(Of PlantRequest)

    Private ReadOnly _plants As IList(Of PlantResponse)

    Public Sub New()
        _plants = New List(Of PlantResponse)
        _plants.Add(New PlantResponse() With {.Id = 1, .LatinName = "Fagus", .Name = "Beuk"})
        _plants.Add(New PlantResponse() With {.Id = 2, .LatinName = "Betula", .Name = "Berk"})
    End Sub

    Public Overrides Function OnGet(ByVal request As PlantRequest) As Object
        If Not String.IsNullOrEmpty(request.Name) Then
            Return _plants.Where(Function(response) response.Name = request.Name)
        End If
        Return _plants
    End Function

End Class
```
As you see I inherit from restservicebase and for now I just override the Onget method. The OnGet takes a plantrequest as a parameter. 

Since I still have the routes setup I can now use this url to get all the plants.

http://localhost:3318/plant

With this as the result.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/servicestack/servicestack3.png?mtime=1342891714"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/servicestack/servicestack3.png?mtime=1342891714" width="661" height="223" /></a>
</div>

Or I can do this and get one result.

http://localhost:3318/plant/Beuk

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/servicestack/servicestack4.png?mtime=1342891886"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/servicestack/servicestack4.png?mtime=1342891886" width="660" height="214" /></a>
</div>

## The easyhttp client

And now it was time to write the client.

So there is 2 ways I could do this with easyhttp last week.

```vbnet
Option Explicit Off

Imports EasyHttp.Http

Module Module1

    Sub Main()
        Dim http = New HttpClient("http://localhost:3318")
        http.Request.Accept = HttpContentTypes.ApplicationJson
        Dim response = http.Get("/plant/Beuk")
        Dim plant = response.DynamicBody
        WritePlant(plant)
        response = http.Get("/plant?Name=Berk")
        plant = response.DynamicBody
        WritePlant(plant)
        Console.ReadLine()
    End Sub

    Private Sub WritePlant(ByVal plant As Object)

        For Each plant In plant
            Console.WriteLine(plant.Id)
            Console.WriteLine(plant.Name)
            Console.WriteLine(plant.LatinName)
        Next
    End Sub
End Module

Public Class PlantRequest
    Public Property Name As String
End Class```
I can live with both ways. And it works. But I wanted a better way.

I wanted this.

```vbnet
Dim response = http.Get("/plant", new With {.Name="Beuk"})```
The above just makes the queryparameters and does the call that way.

So this week there is a version of easyhttp namely (1.5.3) that supports the above. After all easyhttp is opensource and I just forked it, added a ton of code. Did a pull request, did another pull request and did a third pull request (Hadi is a hard man to please) and now we have the above code working. Simples 😉

## Conclusion

If an open source projects doesn&#8217;t work the way you want than just fork it and add it.

 [1]: /index.php/DesktopDev/MSTech/VBNET/servicestack-vb-net-and-some