---
title: 'Nancy and VB.Net: the razor view engine'
author: Christiaan Baes (chrissie1)
type: post
date: 2012-12-12T07:22:00+00:00
ID: 1850
excerpt: Trying Nancy with the Razor viewengine.
url: /index.php/webdev/serverprogramming/nancy-and-vb-net-the/
views:
  - 8055
categories:
  - ASP.NET
  - Server Programming
tags:
  - nancy
  - vb.net

---
## Introduction

Yesterday I [started using Nancy,][1] and I used the SuperSimpleViewEngine but I was told to use the razor view engine instead. And because I&#8217;m a good boy I did that.

## Getting it to work

First thing to do is to install the Nancy.Viewengines.Razor package from nuget. This of course downloads the needed assemblies and adapts your webconfig. 

And it makes a mistake when adapting the webconfig.

Your webconfig will look like this when it is done.

```xml
&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;!--
  For more information on how to configure your ASP.NET application, please visit
  http://go.microsoft.com/fwlink/?LinkId=169433
  --&gt;
&lt;configuration&gt;
  &lt;system.web&gt;
    &lt;compilation debug="true" strict="false" explicit="true" targetFramework="4.5" /&gt;
    &lt;httpRuntime targetFramework="4.5" /&gt;
    &lt;httpHandlers&gt;
      &lt;add verb="*" type="Nancy.Hosting.Aspnet.NancyHttpRequestHandler" path="*" /&gt;
    &lt;/httpHandlers&gt;
    &lt;compilation debug="true" targetFramework="4.0"&gt;
      &lt;buildProviders&gt;
        &lt;add extension=".cshtml" type="Nancy.ViewEngines.Razor.BuildProviders.NancyCSharpRazorBuildProvider, Nancy.ViewEngines.Razor.BuildProviders" /&gt;
        &lt;add extension=".vbhtml" type="Nancy.ViewEngines.Razor.BuildProviders.NancyVisualBasicRazorBuildProvider, Nancy.ViewEngines.Razor.BuildProviders" /&gt;
      &lt;/buildProviders&gt;
    &lt;/compilation&gt;
  &lt;/system.web&gt;
  &lt;system.webServer&gt;
    &lt;validation validateIntegratedModeConfiguration="false" /&gt;
    &lt;handlers&gt;
      &lt;add name="Nancy" verb="*" type="Nancy.Hosting.Aspnet.NancyHttpRequestHandler" path="*" /&gt;
    &lt;/handlers&gt;
  &lt;/system.webServer&gt;
  &lt;appSettings&gt;
    &lt;add key="webPages:Enabled" value="false" /&gt;
  &lt;/appSettings&gt;
&lt;/configuration&gt;```
The problem being that it adds this line <code class="codespan">&lt;compilation debug="true" targetFramework="4.0"&gt;</code> while I already have this line <code class="codespan">&lt;compilation debug="true" strict="false" explicit="true" targetFramework="4.5" /&gt;</code> and that seemed to be undesirable.

I fixed it by removing the first line and changing the second.

```xml
&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;!--
  For more information on how to configure your ASP.NET application, please visit
  http://go.microsoft.com/fwlink/?LinkId=169433
  --&gt;
&lt;configuration&gt;
  &lt;system.web&gt;
    &lt;httpRuntime targetFramework="4.5" /&gt;
    &lt;httpHandlers&gt;
      &lt;add verb="*" type="Nancy.Hosting.Aspnet.NancyHttpRequestHandler" path="*" /&gt;
    &lt;/httpHandlers&gt;
    &lt;compilation debug="true" strict="false" explicit="true" targetFramework="4.5"&gt;
      &lt;buildProviders&gt;
        &lt;add extension=".cshtml" type="Nancy.ViewEngines.Razor.BuildProviders.NancyCSharpRazorBuildProvider, Nancy.ViewEngines.Razor.BuildProviders" /&gt;
        &lt;add extension=".vbhtml" type="Nancy.ViewEngines.Razor.BuildProviders.NancyVisualBasicRazorBuildProvider, Nancy.ViewEngines.Razor.BuildProviders" /&gt;
      &lt;/buildProviders&gt;
    &lt;/compilation&gt;
  &lt;/system.web&gt;
  &lt;system.webServer&gt;
    &lt;validation validateIntegratedModeConfiguration="false" /&gt;
    &lt;handlers&gt;
      &lt;add name="Nancy" verb="*" type="Nancy.Hosting.Aspnet.NancyHttpRequestHandler" path="*" /&gt;
    &lt;/handlers&gt;
  &lt;/system.webServer&gt;
  &lt;appSettings&gt;
    &lt;add key="webPages:Enabled" value="false" /&gt;
  &lt;/appSettings&gt;
&lt;/configuration&gt;```
So now we can move on.

## The Models

I changed the model to PlantModel because I want to use PlantsModel for my collection of Plants.

Here is PlantModel.

```vbnet
Namespace Model
    Public Class PlantModel
        Public Property Id As Integer
        Public Property Name As String
        Public Property LatinName As String
    End Class
End Namespace```
And here is PlantsModel.

```vbnet
Namespace Model
    Public Class PlantsModel
        Public Property Plants As IList(Of PlantModel)
        Public ReadOnly Property NumberOfPlants As Integer
            Get
                Return Plants.Count
            End Get
        End Property
    End Class
End Namespace```
## The views

And now we can make our view. I could not really find a template on my machine for razor views so I made a html page and changed the extension to vbhtml.

And this is my view for the PlantModel.

```html
@Inherits Nancy.ViewEngines.Razor.NancyRazorViewBase(Of WebApplication2.Model.PlantModel)

&lt;!DOCTYPE html&gt;
&lt;html&gt;
&lt;head&gt;
    &lt;title&gt;&lt;/title&gt;
&lt;/head&gt;
&lt;body&gt;
    &lt;h1&gt;Welcome to the plant page&lt;/h1&gt;
    &lt;table&gt;
        &lt;tr&gt;
            &lt;td&gt;Id&lt;/td&gt;
            &lt;td&gt;@Model.Id&lt;/td&gt;
        &lt;/tr&gt;
        &lt;tr&gt;
            &lt;td&gt;Name&lt;/td&gt;
            &lt;td&gt;@Model.Name&lt;/td&gt;
        &lt;/tr&gt;
    &lt;/table&gt;
&lt;/body&gt;
&lt;/html&gt;```
And here is the View for my PlantsModel.

```html
@Inherits Nancy.ViewEngines.Razor.NancyRazorViewBase(Of WebApplication2.Model.PlantsModel)

&lt;!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd"&gt;
&lt;html xmlns="http://www.w3.org/1999/xhtml"&gt;
&lt;head&gt;
    &lt;title&gt;&lt;/title&gt;
&lt;/head&gt;
&lt;body&gt;
    &lt;h1&gt;Welcome to the plants page&lt;/h1&gt;
    &lt;table&gt;
        &lt;tr&gt;&lt;th&gt;Id&lt;/th&gt;&lt;th&gt;Name&lt;/th&gt;&lt;/tr&gt;
        @For Each plant As WebApplication2.Model.PlantModel In Model.Plants
            @&lt;tr&gt;
                &lt;td&gt;@plant.Id&lt;/td&gt;
                &lt;td&gt;@plant.Name&lt;/td&gt;
            &lt;/tr&gt;
        Next
    &lt;/table&gt;
&lt;/body&gt;
&lt;/html&gt;```
Look at the for each I have in there.

## The module

The next is my module, which I would like to split up in two pieces next time. Because small is better, but for now this works.

```vbnet
Imports WebApplication2.Model
Imports Nancy

Public Class PlantsModule
    Inherits NancyModule

    Public Sub New()
        MyBase.Get("/plants") = Function(parameters)
                                    Return View(New PlantsModel() With {.Plants = New List(Of PlantModel) From {New PlantModel() With {.Id = 2, .Name = "test"}}})

                                End Function
        MyBase.Get("/plants/{Id}") = Function(parameters)
                                         If parameters.id = 1 Then
                                             Return View(New PlantModel() With {.Id = 1, .Name = "test"})
                                         Else
                                             Return View(New PlantModel() With {.Id = 2, .Name = "test"})
                                         End If
                                     End Function

    End Sub

End Class```
As you can see I have a Plants route which will show you all the plants. And A plants route which accepts an id which I can read and send the data as requested. Not very good at this point in time but you get the drift.

And these are the results of the Belgian jury.

When using the url http://localhost/plants I get this.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/nancy/nancy6.png?mtime=1355303812"><img alt="" src="/wp-content/uploads/users/chrissie1/nancy/nancy6.png?mtime=1355303812" width="408" height="146" /></a>
</div>

When using the url http://localhost/plants/1 I get this.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/nancy/nancy7.png?mtime=1355303865"><img alt="" src="/wp-content/uploads/users/chrissie1/nancy/nancy7.png?mtime=1355303865" width="430" height="132" /></a>
</div>

And when using http://localhost/plants/abc I get an exception of course.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/nancy/nancy9.png?mtime=1355303932"><img alt="" src="/wp-content/uploads/users/chrissie1/nancy/nancy9.png?mtime=1355303932" width="605" height="246" /></a>
</div>

And this funny little fellow on my page.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/nancy/nancy8.png?mtime=1355303979"><img alt="" src="/wp-content/uploads/users/chrissie1/nancy/nancy8.png?mtime=1355303979" width="537" height="294" /></a>
</div>

## Conclusion

Everything seemed to be reasonable, lost a little time with the webconfig thing and looking up syntax for razor, but it was pretty much ok and smooth for the rest.

 [1]: /index.php/WebDev/ServerProgramming/ASPNET/nancy-and-vb-net