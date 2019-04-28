---
title: 'Nancy and VB.Net: Using easyhttp as our client'
author: Christiaan Baes (chrissie1)
type: post
date: 2012-12-22T05:21:00+00:00
ID: 1877
excerpt: Using easyhttp as our client, while still being able to use our browser as our client.
url: /index.php/webdev/serverprogramming/nancy-and-vb-net-using/
views:
  - 5873
categories:
  - ASP.NET
  - Server Programming
tags:
  - nancy
  - vb.net

---
## Introduction

Up until now we have used the browser to look at our data. In other words Nancy created a view for us and returned html to the client that requested that information. But wouldn&#8217;t it be nice if we could use the same for returning html and/or other formats like json or xml. It turns out to be very easy to do with Nancy.

The code can still be found [on github][1].

## Content negotiation

This can only work if Nancy is able to change the response based on the request. In other words [content negotiation][2].

According to their wiki.

> The content negotiation pipeline will inspect the incoming Accept headers and determine which of the requested media types is the most suitable and format the response accordingly.

## The server

For this to work we need to change our Module just a little bit.

Lets take the treesmodule. And change the Get that returns all trees from this 

```vbnet
MyBase.Get("/trees") = Function(parameters)
                                       Return View(New TreesModel() With {.Trees = treeService.AllTrees()})
                                   End Function
            ```
to this

```vbnet
MyBase.Get("/trees") = Function(parameters)
                                       Return Negotiate.WithModel(New TreesModel() With {.Trees = treeService.AllTrees()})
                                   End Function
            ```
Instead of returning a View I now use Negotiate.WithModel and that&#8217;s all the changes I made.

## Easyhttp client

YOu can now make a newproject and add easyhttp to that and read the list of trees like this.

```vbnet
Imports System.Net
Imports EasyHttp.Http

Module Module1

    Sub Main()
        Dim http = New HttpClient()
        http.Request.Accept = HttpContentTypes.ApplicationJson
        Dim trees = http.Get("http://localhost:55360/trees")
        For Each t In trees.DynamicBody.Trees
            Console.WriteLine(t.Id)
            Console.WriteLine(t.Genus)
        Next
        Console.ReadLine()
    End Sub

End Module
```
With this as the result.

> 1
  
> Fagus
  
> 2
  
> Quercus
  
> 3
  
> Betula

And of course all this time the browser client will just keep on working like nothing happened.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/nancy/nancy11.png?mtime=1356160832"><img alt="" src="/wp-content/uploads/users/chrissie1/nancy/nancy11.png?mtime=1356160832" width="396" height="272" /></a>
</div>

## Parameters

We can now also change our second Get in that module to return the data based on the id. To this.

```vbnet
MyBase.Get("/trees/{Id}") = Function(parameters)
                                            Dim result As Integer
                                            Dim isInteger = Integer.TryParse(parameters.id, result)
                                            Dim tree = treeService.FindById(result)
                                            If isInteger AndAlso tree IsNot Nothing Then
                                                Return Negotiate.WithModel(tree)
                                            Else
                                                Return HttpStatusCode.NotFound
                                            End If
                                        End Function
           ```
Again just change the return view call to return Negotiate.WithModel.

Simples.

We can now add an easyhttp call like this.

```vbnet
Dim result = http.Get("http://localhost:55360/trees", New With {.Id = 1})
        Dim tree = result.DynamicBody
        Console.WriteLine(tree.Id)
        Console.WriteLine(tree.Genus)
```
Sadly that won&#8217;t work. Because easyhttp translates that call to http://localhost/trees?id=1 and Nancy wants you to use routes instead. Like this http://localhost/trees/1 . I know how easyhttp does this too, since it was me who [added that feature][3] to easyhttp ;-). So I guess I will have to change easyhttp to use it this way.

Anyway if you change your easyhttp call to this

```vbnet
Dim result = http.Get("http://localhost:55360/trees/1")
        Dim tree = result.DynamicBody
        Console.WriteLine(tree.Id)
        Console.WriteLine(tree.Genus)```
it will work, until I have time to harass Hadi enough so that he does the change or (more likely) until I do the change myself.

## Conclusion

It is extremely easy to make Nancy return the response formatted based on the request.

 [1]: https://github.com/chrissie1/NancyVB
 [2]: https://github.com/NancyFx/Nancy/wiki/Content%20negotiation
 [3]: /index.php/ITProfessionals/ProfessionalDevelopment/my-very-first-pull-request