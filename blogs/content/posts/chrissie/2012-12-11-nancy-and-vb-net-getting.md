---
title: 'Nancy and VB.Net: getting data in your page'
author: Christiaan Baes (chrissie1)
type: post
date: 2012-12-11T12:07:00+00:00
ID: 1848
excerpt: Showing some data on our page the Nancy way.
url: /index.php/webdev/serverprogramming/nancy-and-vb-net-getting/
views:
  - 2722
categories:
  - ASP.NET
  - Server Programming
tags:
  - nancy
  - vb.net

---
## Introduction

A few hours ago I started [a website in Nancy][1]. Now I want to show data I have in my webpage.

And here the docs were not all that helpfull. But I made it.

## The Model

First of all I need a Model.

```
Namespace Model
    Public Class PlantsModel
        Public Property Id As Integer
        Public Property Name As String
        Public Property LatinName As String
    End Class
End Namespace```
And I put that in a Model folder. I ended with Model since that is the convention but not really needed in all cases.

## The View

Time to move on to more exiting things.

I created a Plants.html file.

```html
&lt;!DOCTYPE html&gt;
&lt;html&gt;
&lt;head&gt;
    &lt;title&gt;NancyFX&lt;/title&gt;
&lt;/head&gt;
&lt;body&gt;
    &lt;h1&gt;Plants&lt;/h1&gt;
    &lt;div id="plants"&gt;
        &lt;p&gt;@Model.Id&lt;/p&gt;
        &lt;p&gt;@Model.Name&lt;/p&gt;
    &lt;/div&gt;
&lt;/body&gt;
&lt;/html&gt;
    ```
I used the [SuperSimpleViewEngine][2] for this.

As you see I just add the @Model. and then the name of the property I want. Simples.

Now I just need to tell my controller what to do.

## The Controller

I guess we can call our module our controller.

```vbnet
Imports WebApplication2.Model
Imports Nancy

Public Class PlantsModule
    Inherits NancyModule

    Public Sub New()
        MyBase.Get("/plants") = Function(parameters)
                                    Return View(New PlantsModel() With {.Id = 2, .Name = "test"})
                                End Function

    End Sub

End Class```
So the url http://localhost/plants will take you to the page Plants.htmland will inject the PlantsModel I just created in there. By convention the fact that you inject a PlanstModel in there informs Nancy that you want to use the Plant.html view. 

You could have written this instead.

```vbnet
Imports WebApplication2.Model
Imports Nancy

Public Class PlantsModule
    Inherits NancyModule

    Public Sub New()
        MyBase.Get("/plants") = Function(parameters)
                                    Return View("Plants.html", New PlantsModel() With {.Id = 2, .Name = "test"})
                                End Function

    End Sub

End Class```
Or any other html.

And that gives us this amazing page.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/nancy/nancy5.png?mtime=1355234722"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/nancy/nancy5.png?mtime=1355234722" width="230" height="243" /></a>
</div>

## Conclusion

Slightly harder than it should have been because I could not find the correct docs that explained this simply. But I got it working anyway.

 [1]: /index.php/WebDev/ServerProgramming/ASPNET/nancy-and-vb-net
 [2]: https://github.com/NancyFx/Nancy/wiki/View-engines