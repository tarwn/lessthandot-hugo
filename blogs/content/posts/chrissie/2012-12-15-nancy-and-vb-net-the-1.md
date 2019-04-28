---
title: 'Nancy and VB.Net: The bootstrapper'
author: Christiaan Baes (chrissie1)
type: post
date: 2012-12-15T06:27:00+00:00
ID: 1858
excerpt: Adding a bootstrapper to my project and finding out why it is not being picked up.
url: /index.php/webdev/serverprogramming/nancy-and-vb-net-the-1/
views:
  - 4814
categories:
  - ASP.NET
  - Server Programming
tags:
  - nancy
  - vb.net

---
## Introduction

I have been playing with Nancy for a few days now. And yesterday was an extremely bad day. I was adding layouts so I could have a masterpage and I was adding services so I could get some data and save some data. I wanted to save my data n a collection and keep it in memory just for now. And I needed a bootstrapper for that (I&#8217;ll explain why a bit later. But my bootstrapper never got loaded and making the razor views was a pain because I never got to see the errormessages why a view was failing. So anyway here is the story about the bootstrapper that got lost.

All the code is on [Github][1] now. And yes I also have a C# version of that code in the repo.

## The why

So I needed a service to keep a collection of plants/trees/bushes and then I needed to inject that service into my module so I could read the collection and add to it. 

This is such a service.

```vbnet
Imports WebApplication2.Model

Namespace Services
    Public Class TreeService

        Private ReadOnly _trees As IList(Of TreeModel)

        Public Sub New()
            _trees = New List(Of TreeModel)
            _trees.Add(New TreeModel() With {.Id = 1, .Genus = "Fagus"})
            _trees.Add(New TreeModel() With {.Id = 2, .Genus = "Quercus"})
            _trees.Add(New TreeModel() With {.Id = 3, .Genus = "Betula"})
        End Sub

        Public Function AllTrees() As IList(Of TreeModel)
            Return _trees
        End Function

        Public Function FindById(ByVal id As Integer) As TreeModel
            Return _trees.SingleOrDefault(Function(x) x.Id = id)
        End Function
    End Class
End Namespace```
And this is my adapted module.

```vbnet
Imports WebApplication2.Services
Imports WebApplication2.Model
Imports Nancy

Namespace Modules

    Public Class TreesModule
        Inherits NancyModule

        Public Sub New(ByVal treeService As TreeService)
            MyBase.Get("/trees") = Function(parameters)
                                       Return View(New TreesModel() With {.Trees = treeService.AllTrees()})

                                   End Function
            MyBase.Get("/trees/{Id}") = Function(parameters)
                                            Dim result As Integer
                                            Dim isInteger = Integer.TryParse(parameters.id, result)
                                            Dim tree = treeService.FindById(result)
                                            If isInteger AndAlso tree IsNot Nothing Then
                                                Return View(tree)
                                            Else
                                                Return HttpStatusCode.NotFound
                                            End If
                                        End Function

        End Sub

    End Class
End Namespace```
As you can see I inject my TreeService into the module and read from that. In this case that&#8217;s just fine and work as advertised. The treeservice is magically instantiated because Nancy uses TinyIoc behind the scenes to instantiate andinject it into my module. Easy as pie.

But now it is time to give the user the abality to add a tree to our collection.

For this I need to add this method to the TreeService.

```vbnet
Public Sub Add(ByVal treeModel As TreeModel)
            _trees.Add(treeModel)
        End Sub```
and this bit of code to the TreesModule.

```vbnet
MyBase.Get("/trees/add/") = Function(parameters)
                                            Return View("AddTree.vbhtml", New TreeModel)
                                        End Function
            MyBase.Post("/trees/add/") = Function(parameters)
                                             Dim tree = Me.Bind(Of TreeModel)()
                                             treeService.Add(tree)
                                             Return Response.AsRedirect("/trees")
                                         End Function```
As you can see I added a post and a get and the get uses an addtree template. which I have here. It uses layouts to which I will get back on a later date.

```vbnet
@Inherits Nancy.ViewEngines.Razor.NancyRazorViewBase(Of WebApplication2.Model.TreeModel)

@Code
    ViewBag.Title = "Add tree page"
    Layout = "Master.vbhtml"
End Code

@Section menu
    RenderSection("menu")
End Section
@Section menuitems
    &lt;a href="/trees"&gt;Trees&lt;/a&gt;
End Section

    &lt;h1&gt;Welcome to the add tree page&lt;/h1&gt;
    &lt;form  action="/trees/add/" method="post"&gt;
        &lt;table&gt;
            &lt;tr&gt;
                &lt;td&gt;Id&lt;/td&gt;
                &lt;td&gt;&lt;input type="text" name="Id" value="@Model.Id" /&gt;&lt;/td&gt;
            &lt;/tr&gt;
            &lt;tr&gt;
                &lt;td&gt;Genus&lt;/td&gt;
                &lt;td&gt;&lt;input type="text" name="Genus" value="@Model.Genus" /&gt;&lt;/td&gt;
            &lt;/tr&gt;
        &lt;/table&gt;
        &lt;input type="submit" value="Add new"/&gt;
    &lt;/form&gt;    
```
And the above kind of works. It adds the tree to the collection. 

There is however a slight problem. after the submit we go back to the Trees list page and we will not see our newly added tree. Why? Because TinyIoC will create a new instance per request by default. We can however change that behavior in the bootstrapper.

For this we just create a class that inherits from DefaultNancyBootstrapper like this.

```vbnet
Imports Nancy.Hosting.Aspnet
Imports Nancy
Imports Nancy.TinyIoc
Imports WebApplication2.Services

Public Class MyBootStrapper
    Inherits DefaultNancyBootstrapper

    Protected Overrides Sub ConfigureApplicationContainer(container As TinyIoCContainer)
        container.Register(Of BushService).AsSingleton()
        container.Register(Of TreeService).AsSingleton()
    End Sub
End Class
```
Nancy should now just pick up this custom bootstrapper and start using it. And it does. But there is a caveat and I spent best part of a few hours figuring out why it didn&#8217;t.

## The problem

So why did Nancy not find my bootstrapper and use it?

And this is where open source comes in very handy. I just downloaded the source of the assemblies I needed and added breakpoints. It was made very easy by the very clean and readable code Nancy provides for us. Kudos to them.

The problem is Nancy.Testing. Because I was using this project to write a blogpost I was more lazy than usual and did not create a separate project for my testing needs, I just created a Tests folder and put them all in there and added a reference to Nancy.Testing.

So after adding the source code it was easy for me to go through it and put some breakpoints in.

My main suspect had been NancyBootstrapperLocator from the beginning, but first I thought that it could have problems with my VB code, which was not the case BTW.

And this was the result.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/nancy/nancy10.png?mtime=1355559642"><img alt="" src="/wp-content/uploads/users/chrissie1/nancy/nancy10.png?mtime=1355559642" width="858" height="163" /></a>
</div>

AS you can see my Bootstrapper is in the list of locatedbootstrapper but it is second in the list after the Nancy.Testing configurable bootstrapper. And Nancy will take the first one out of that list so mine was ignored.

The fix is easy enough. Just create a separate Testproject.

## Conclusion

Open source is handy when you are having problems. I went to github first to look at the code to see if could see any obvious problems and then I downloaded the source so I could step through it to find the problem. And I did. And the problem was easily fixed.

 [1]: https://github.com/chrissie1/NancyVB