---
title: Servicestack, VB.Net and some easyhttp
author: Christiaan Baes (chrissie1)
type: post
date: 2012-07-12T10:04:00+00:00
ID: 1671
excerpt: Trying out Servicestack with VB.Net and easyhttp as the client.
url: /index.php/desktopdev/mstech/servicestack-vb-net-and-some/
views:
  - 7874
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - easyhttp
  - servicestack
  - vb.net

---
## Introduction

This morning I read a tweet by [@ONE75][1] (Stijn Volders) about a [blogpost][2] by [Phillip Haydon][3] ([@philliphaydon][4]).

The blogpost is about [servicestack][5].

> Service Stack is a high-performance .NET web services framework (including a number of high-performance sub-components: see below) that simplifies the development of XML, JSON, JSV and WCF SOAP Web Services. For more info check out servicestack.net.

So I went out and tried it.

## The server

First I set out to create the server. So I created an Empty ASP.Net web application. 

And then you add the nuget package.

I used <code class="codespan">Install-package ServiceStack</code>

And I changed the web.config to this.

```xml
&lt;?xml version="1.0"?&gt;

&lt;!--
  For more information on how to configure your ASP.NET application, please visit
  http://go.microsoft.com/fwlink/?LinkId=169433
  --&gt;

&lt;configuration&gt;
    &lt;system.web&gt;
        &lt;compilation debug="true" strict="false" explicit="true" targetFramework="4.0" /&gt;
    &lt;/system.web&gt;
  &lt;system.web&gt;
    &lt;httpHandlers&gt;
      &lt;add path="*" type="ServiceStack.WebHost.Endpoints.ServiceStackHttpHandlerFactory, ServiceStack" verb="*"/&gt;
    &lt;/httpHandlers&gt;
  &lt;/system.web&gt;

  &lt;!-- Required for IIS 7.0 --&gt;
  &lt;system.webServer&gt;
    &lt;validation validateIntegratedModeConfiguration="false" /&gt;
    &lt;handlers&gt;
      &lt;add path="*" name="ServiceStack.Factory" type="ServiceStack.WebHost.Endpoints.ServiceStackHttpHandlerFactory, ServiceStack" verb="*" preCondition="integratedMode" resourceType="Unspecified" allowPathInfo="true" /&gt;
    &lt;/handlers&gt;
  &lt;/system.webServer&gt;
&lt;/configuration&gt;
```
After that I added a Global.asax. And changed it to this.

```vbnet
Imports System.Web.SessionState
Imports Funq
Imports ServiceStackModel.Request
Imports ServiceStack.WebHost.Endpoints

Public Class Global_asax
    Inherits System.Web.HttpApplication

    Public Class HelloAppHost
        Inherits AppHostBase

        Public Sub New()
            MyBase.New("Plant Web Services", GetType(PlantService).Assembly)
        End Sub
        
        Public Overrides Sub Configure(container As Container)
            Routes.Add(Of PlantRequest)("/plant").Add(Of PlantRequest)("/plant/{Name}")
        End Sub
    End Class

    Sub Application_Start(ByVal sender As Object, ByVal e As EventArgs)
        Dim apphost = New HelloAppHost()
        apphost.Init()
    End Sub

End Class```
The routes are optional, I think, but useful later on in this blogpost.

I then added a classlibrary for my model.

Which contains 2 files.

The Request and the response.

```vbnet
Namespace Request
    Public Class PlantRequest
        Public Property Name As String
    End Class
End NameSpace```
```vbnet
Namespace Response
    Public Class PlantResponse
        Public Property Name As String
    End Class
End NameSpace```
And now for the most important class, the service itself.
  
Very simple service, return the name with the name of the request attached to that.

```vbnet
Imports ServiceStackModel.Response
Imports ServiceStackModel.Request
Imports ServiceStack.ServiceHost

Public Class PlantService
    Implements IService(Of PlantRequest)

    Public Function Execute(request As PlantRequest) As Object Implements ServiceStack.ServiceHost.IService(Of PlantRequest).Execute
        Return New PlantResponse With {.Name = "Name: " & request.Name}
    End Function

End Class
```
If you run this you should get this.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/servicestack/servicestack1.png?mtime=1342093847"><img alt="" src="/wp-content/uploads/users/chrissie1/servicestack/servicestack1.png?mtime=1342093847" width="687" height="547" /></a>
</div>

Because of the routes we can also surf to http://localhost:3318/plant/test and get this.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/servicestack/servicestack2.png?mtime=1342093992"><img alt="" src="/wp-content/uploads/users/chrissie1/servicestack/servicestack2.png?mtime=1342093992" width="654" height="193" /></a>
</div>

But now we also want to get that data in our client code.

## Servicestack client

You can use the servicestack clients if you so desire. For this to work your client will also need a reference to your model.

And you will need the nuget package ServiceStack.Common. 

Making it work is fairly easy.

```vbnet
Imports ServiceStackModel.Request
Imports ServiceStackModel.Response
Imports ServiceStack.ServiceClient.Web

Module Module1

    Sub Main()
        Dim client = New JsonServiceClient("http://localhost:3318")
        Dim response = client.Send(Of PlantResponse)(New PlantRequest With {.Name = "World!"})
        Console.WriteLine(response.Name)
        Console.ReadLine()
    End Sub

End Module
```
You send an object of the request type and get one back of the response type. Easy as pie.

## The easyhttp client

Of course this is just a webservice so you can use other clients to get your result.

I used [easyhttp][6].

And I used the routes I configured earlier to get my result.

```vbnet
Imports EasyHttp.Http

Module Module1

    Sub Main()
        Dim http = New HttpClient()
        http.Request.Accept = HttpContentTypes.ApplicationJson
        Dim response = http.Get("http://localhost:3318/plant/test?format=json")
        Dim plant = response.DynamicBody
        Console.WriteLine(plant.Name)
        Console.ReadLine()
    End Sub

End Module
```
With this approach I don&#8217;t need a reference to the Model I created earlier I just use Dynamic objects. 

## The code

You can find the [code on github][7]

## Conclusion

Servicestack is easy to setup and get working. Getting the results from your services is easy to. All this provided that you have the web development things installed for Visual studio ;-), which I hadn&#8217;t.

 [1]: https://twitter.com/ONE75/
 [2]: http://www.philliphaydon.com/2012/02/service-stack-i-heart-you-my-conversion-from-wcf-to-ss/
 [3]: http://www.philliphaydon.com/
 [4]: https://twitter.com/#!/philliphaydon
 [5]: https://github.com/ServiceStack
 [6]: https://github.com/hhariri/EasyHttp
 [7]: https://github.com/chrissie1/ServicestackDemo