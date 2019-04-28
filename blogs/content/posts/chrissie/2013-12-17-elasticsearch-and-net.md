---
title: Elasticsearch and .Net
author: Christiaan Baes (chrissie1)
type: post
date: 2013-12-17T09:23:00+00:00
ID: 2210
excerpt: Elasticsearch and .Net using NEST.
url: /index.php/datamgmt/datadesign/elasticsearch-and-net/
views:
  - 15810
categories:
  - Data Modelling and Design
tags:
  - .net
  - elasticsearch
  - vb.net

---
## Introduction

In my quest to learn a bit more about Elasticsearch ([post 1][1] en [post 2][2]) I will now use a .Net framework to connect to the server.

I have found that [NEST][3] is the most popular out there and featurecomplete. Of course there is a [blogpost by Joel Abrahamsson][4] out there but it is a bit dated.

## The code

Let me begin by making a model.

```vbnet
Class Fiber
    Public Property Id As Integer
    Public Property FiberColor As FiberColorEnum
    Public Property Remarks As String
    Public Property Msps As IList(Of Msp)

    Public Enum FiberColorEnum
        Orange
        Red
        Brown
        Blue
        Green
        Black
        NoColor
    End Enum
End Class

Class Msp
    Public Property Id As Integer
    Public Property A As Decimal
    Public Property WaveLength As Decimal
End Class```
And now that I have that out of the way I can make a connection to the server.

```vbnet
Dim setting = New ConnectionSettings(New Uri("http://192.168.73.128:9200")).SetDefaultIndex("fibers")
Dim client = New ElasticClient(setting)
```
<span class="MT_red">Apparently the setdefaultindex is important because without it you get all kinds of errors all telling you to set a default index. Don&#8217;t know why that isn&#8217;t in the constructor as a parameter.</span>

But now that we have a client we can add thins to the index.

```vbnet
Dim fiber As New Fiber With {.Id = 1, .Remarks = "Test", .FiberColor = fiber.FiberColorEnum.Black, .Msps = New List(Of Msp) From {New Msp With {.Id = 1, .A = 0.1D, .WaveLength = 380}, New Msp With {.Id = 2, .A = 0.11D, .WaveLength = 381}}}
client.Index(Of Fiber)(fiber)
        ```
Of course putting the enum in there is a bad idea since we are creating a full text index and we won&#8217;t find what we are looking for since it will have an int in there and not some string but I&#8217;ll try to solve that in a latter episode.

Anyway we can now Get that object by doing 

```vbnet
client.Get(Of Fiber)(1)```
Which will give you the _source object from the result when you do GET /fibers/fibers/1 via the REST interface.

> {
     
> &#8220;_index&#8221;: &#8220;fibers&#8221;,
     
> &#8220;_type&#8221;: &#8220;fibers&#8221;,
     
> &#8220;_id&#8221;: &#8220;1&#8221;,
     
> &#8220;_version&#8221;: 5,
     
> &#8220;exists&#8221;: true,
     
> &#8220;_source&#8221;: {
        
> &#8220;id&#8221;: 1,
        
> &#8220;fiberColor&#8221;: 5,
        
> &#8220;remarks&#8221;: &#8220;Test&#8221;,
        
> &#8220;msps&#8221;: [
           
> {
              
> &#8220;id&#8221;: 1,
              
> &#8220;a&#8221;: 0.1,
              
> &#8220;waveLength&#8221;: 380
           
> },
           
> {
              
> &#8220;id&#8221;: 2,
              
> &#8220;a&#8221;: 0.11,
              
> &#8220;waveLength&#8221;: 381
           
> }
        
> ]
     
> }
  
> }

If you want to get the full object you need to use GetFull.

```vbnet
client.GetFull(Of Fiber)(1)```
And now you have the full object as shown in the REST response. So you now show that.

```vbnet
Private Sub PrintFiber(ByVal fiberResponse As IGetResponse(Of Fiber))
        Console.WriteLine("Id: " & fiberResponse.Id)
        If fiberResponse.Exists Then
            Console.WriteLine("Id: " & fiberResponse.Source.Remarks)
            Console.WriteLine("FiberColor: " & fiberResponse.Source.FiberColor)
            Console.WriteLine("Msps: " & fiberResponse.Source.Msps.Count)
        End If
        Console.WriteLine("Connectionstatus: " & fiberResponse.ConnectionStatus.Success)
        Console.WriteLine("Exists: " & fiberResponse.Exists)
        Console.WriteLine("Version: " & fiberResponse.Version)
    End Sub```
Which shows you that GetFull returns an object of IGetResponse(Of T).

## Conclusion

That was a nice start and very easy to get to grips with, although the difference between Get and GetFull was not very clear to me in the beginning. Next up is search.

 [1]: /index.php/DataMgmt/DataDesign/elasticsearch
 [2]: /index.php/DataMgmt/DataDesign/elastic-hq
 [3]: https://github.com/Mpdreamz/NEST
 [4]: http://joelabrahamsson.com/extending-aspnet-mvc-music-store-with-elasticsearch/