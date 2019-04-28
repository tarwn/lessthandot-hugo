---
title: 'Me and ASP.Net MVC: even less magic strings but opinionated'
author: Christiaan Baes (chrissie1)
type: post
date: 2009-03-20T08:29:23+00:00
ID: 356
url: /index.php/webdev/webdesigngraphicsstyling/me-and-asp-net-mvc-even-less-magic-strin/
views:
  - 2665
categories:
  - Web Design, Graphics and Styling
tags:
  - asp.net mvc
  - 'c#'

---
In my [previous post][1] (not so long ago). I talked about the Actionlink and it not being very refactor friendly because of all the magic strings in it. 

But there was a strongly typed version out there somewhere that didn&#8217;t work all the time but I couldn&#8217;t find it at that time.

Well it is hiding [here][2] and you can dowload it by clicking on the ASP.NET MVC v1.0 Futures link.

This is a dll so store it somewhere in your project and then reference itfrom your project. The dll and the namespace cary the name Microsoft.Web.Mvc unlike the real Mvc which resides in System.Web.Mvc. 

Now how do we get our strongly typed Actionlink to work. Simple.

First we make sure our method doesn&#8217;t have the ActionName attribute on it. 

```csharp
public ActionResult Person()
        {
            var person = personRepository.FindPerson(1);
            return View("Person",person);
        }```
And then in our Site.Master page we add this somewhere near the top.

```asp
&lt;%@ Import Namespace="Microsoft.Web.Mvc"%&gt;
```
And now both these links produce the same result.

```asp
&lt;li&gt;&lt;%= Html.ActionLink("Person", "Person", "Person")%&gt;&lt;/li&gt;
                    &lt;li&gt;&lt;%= Html.ActionLink&lt;PersonController&gt;(e =&gt; e.Person(),"Person")%&gt;&lt;/li&gt;
                    ```
Now if I refactor the Method name Person to PersonPage for instance.

```csharp
public ActionResult PersonPage()
        {
            var person = personRepository.FindPerson(1);
            return View("Person",person);
        }```
it will also rename the function for me or give a compile error which we prefer over a runtime error.

```asp
&lt;li&gt;&lt;%= Html.ActionLink("Person", "Person", "Person")%&gt;&lt;/li&gt;
                    &lt;li&gt;&lt;%= Html.ActionLink&lt;PersonController&gt;(e =&gt; e.PersonPage(),"Person")%&gt;&lt;/li&gt;
                    ```
So now the first line doesn&#8217;t work but the second still does without much effort.

 [1]: /index.php/WebDev/ServerProgramming/me-and-asp-net-mvc-getting-to-grips-with
 [2]: http://aspnet.codeplex.com/Release/ProjectReleases.aspx?ReleaseId=24471