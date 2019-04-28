---
title: 'Me and ASP.Net MVC: getting to grips with the ActionLink'
author: Christiaan Baes (chrissie1)
type: post
date: 2009-03-20T07:53:32+00:00
ID: 355
url: /index.php/webdev/serverprogramming/me-and-asp-net-mvc-getting-to-grips-with/
views:
  - 1948
categories:
  - ASP.NET
  - Server Programming
tags:
  - asp.net mvc
  - 'c#'

---
I installed the brandnew (nearly anyway) [release version of ASP.Net MVC v1.0][1]. I don&#8217;t think that will cahnge much for my previous examples since the code all still works. Apart from having to reset the references that is. 

So in my previous example I removed some of the magic strings. But it seems that some other magic strings are here to stay for a while at least. 

And this is it.

```csharp
&lt;li&gt;Html.ActionLink("Home", "Index", "Home")&lt;/li&gt;```
The autolink used to have a strongly typed version in some preview releases. 

```csharp
Html.ActionLink&lt;EquipmentController&gt;(c=&gt;c.Show(equipment.Number, equipment.Letter), "See")```
But they dropped that for some reasons. And I&#8217;ll try to explain why. 

First of all lets go back to our PersonController and the actionlink that I used that uses that controller.

```csharp
Html.ActionLink("Person", "Person", "Person")```
As I said before the first parameter is the text so no problem there that is supposed to be a string (more or less). The second parameter is the ActionName and the third is the Controller Name without the word Controller at the end.

So if we take the strongly type version of that it would be.

```csharp
Html.ActionLink&lt;PersonController&gt;(c=&gt;c.Person(), "Person")```
That seems very ok and it would work too. What the problem is is that you can do this in the controller.

```csharp
[System.Web.Mvc.ActionName("NavigatePerson")]
        public ActionResult Person()
        {
            var person = personRepository.FindPerson(1);
            return View("Person",person);
        }```
In other words the ActionName is no longer th same as the MethodName, by default they are. So the not so strongly typed ActionLink now would look like this.

```csharp
Html.ActionLink("Person", "NavigatePerson", "Person")```
And that would make that this would no longer work.

```csharp
Html.ActionLink&lt;PersonController&gt;(c=&gt;c.Person(), "Person")```
Since what they are in fact doing in the strongly typed version was to make the magic strings for you. But that doesn&#8217;t work anymore if you change the name of the method.

So what is the solution? I would say create opinionated code. Meaning that you make a rule that says don&#8217;t use that attribute so that methodname and actionname will always be the same that way you can still use the strongly typed version of that actionlink. 

Now I just have to find a way to get that back in since the strongly typed version is nowhere to be found.

 [1]: http://go.microsoft.com/fwlink/?LinkId=144444