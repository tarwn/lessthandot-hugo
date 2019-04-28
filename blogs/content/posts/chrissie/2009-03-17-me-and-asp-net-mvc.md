---
title: Me and ASP.Net MVC
author: Christiaan Baes (chrissie1)
type: post
date: 2009-03-17T07:29:46+00:00
ID: 349
url: /index.php/webdev/serverprogramming/aspnet/me-and-asp-net-mvc/
views:
  - 4065
categories:
  - ASP.NET
tags:
  - asp.net mvc
  - 'c#'
  - vb.net

---
Let me start by saying that I&#8217;m not a webdeveloper I do most of my work in Winforms. I personaly think webdevelopment is a bit frustrating. The way you have to test for x number of browsers just to find out there is another browser ou there you never heard f and that doesn&#8217;t render your site like it should. But anyway. I had a stab at [ASP.Net MVC][1] just to see how they implement MVC (Model-View-Controller). 

I must add at this point that I had previous experience with [JSF][2] and I kinda liked it. But what I didn&#8217; like was the fact that the MV and C were pretty closely coupled. Especialy the C and the V. Personaly I would like them a little less coupled so that I can use my M with another V and perhaps even have the C with another V. I understand that certain technologies work in a different way and that they really don&#8217;t mix and match but in an ideal world that should be possible. If for example I want a winforms application that has the same functionality as a web application. I really don&#8217;t want to write all the controllers and models twice just the view. Just put everything else that is reusable in one assembly and have two view components. ANd I&#8217;m not saying everything will be or needs to be reusable. I just want to reuse as much as possible.

Here we go. First thing to do is [download the stuff][3]. And install it of course. 

Then I startup VS2008 and I get the option to make a new asp.net MVC project.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/WebDev/mvcnewproject.jpg" alt="" title="" width="691" height="424" />
</div>

So I do that. And then it asks me if I want a Test project to go with that.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/WebDev/image004.jpg" alt="" title="" width="547" height="363" />
</div>

I only get to choose a Visual studio unit test. But I don&#8217;t really want that, allthough I am curious what it will do so I create it anyway.

First thing it does is create a whole bunch of folders and files that it things I will need and that serve as an example. It has also created some unittests for me, for the things that are already there. Ok, looks pretty cool. I can even run it. And it works.
  
This is the main page.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/WebDev/mvcnewproject1.jpg" alt="" title="" width="1082" height="274" />
</div>

So now I want to add my own page to the mix. I want to add the Person page. A page with a lastanme and a firstname, because our Person only has those two attributes.

Here is what our Person looks like in VB.Net.

```vbnet
Namespace Model
    Public Class Person

        Private _firstName As String
        Private _lastName As String

        Public Sub New(ByVal FirstName As String, ByVal LastName As String)
            _firstName = FirstName
            _lastName = LastName
        End Sub

        Public Property FirstName() As String
            Get
                Return _firstName
            End Get
            Set(ByVal value As String)
                _firstName = value
            End Set
        End Property

        Public Property LastName() As String
            Get
                Return _lastName
            End Get
            Set(ByVal value As String)
                _lastName = value
            End Set
        End Property

        Public Overrides Function ToString() As String
            Return _lastName & " " & _firstName
        End Function

        Public Overrides Function Equals(ByVal obj As Object) As Boolean
            If TypeOf obj Is Person OrElse obj IsNot Nothing Then
                Return (_lastName & " " & _firstName).Equals(CType(obj, Person)._lastName & " " & CType(obj, Person)._firstName)
            Else
                Return False
            End If
        End Function

        Public Overrides Function GetHashCode() As Integer
            Return (_lastName & " " & _firstName).GetHashCode()
        End Function
    End Class
End Namespace```
Simple but clean.

But for the moment I want to take it one step at a time. So I want to have an extra menu item named Person on the menu page.

I found the menuitems (Home, About) in the Views/Shared/Site.Master file. And it looks like this.

```html
&lt;div id="menucontainer"&gt;
            
                &lt;ul id="menu"&gt;              
                    &lt;li&gt;&lt;%= Html.ActionLink("Home", "Index", "Home")%&gt;&lt;/li&gt;
                    &lt;li&gt;&lt;%= Html.ActionLink("About", "About", "Home")%&gt;&lt;/li&gt;
                &lt;/ul&gt;
            
            &lt;/div&gt;```
So I guess I just have to add my own to make it work.

```html
&lt;div id="menucontainer"&gt;
            
                &lt;ul id="menu"&gt;              
                    &lt;li&gt;&lt;%= Html.ActionLink("Home", "Index", "Home")%&gt;&lt;/li&gt;
                    &lt;li&gt;&lt;%= Html.ActionLink("Person", "Person", "Person")%&gt;&lt;/li&gt;
                    &lt;li&gt;&lt;%= Html.ActionLink("About", "About", "Home")%&gt;&lt;/li&gt;
                &lt;/ul&gt;
            
            &lt;/div&gt;```
How does this ActionLink work. Well according to the intelisense (who needs a manual anyway) The first magic string is the tag that will be shown on the page. The second is the action and the third is the controller. 

So now we have this.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/WebDev/mvcnewproject2.jpg" alt="" title="" width="326" height="124" />
</div>

I can live with that.

Of course when I click on it nothing happens since I have no controller yet. I get this errormessage. Which is in Dutch for some reason. But in short it says it can&#8217;t find Person/Person.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/WebDev/mvcnewproject3.jpg" alt="" title="" width="1070" height="220" />
</div>

Which means it can&#8217;t find a controller.

So lets create PersonController with an action Person on it.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.Mvc;

namespace MvcApplication1.Controllers
{
    [HandleError]
    public class PersonController : Controller
    {
        public ActionResult Person()
        {
            return View();
        }
    }
}
```
That should work, right?

No, it doesn&#8217;t because a controller also needs a view. In this Case called Person. If we click on it we get this errormessage. 

> Serverfout in toepassing /.
  
> The view &#8216;Person&#8217; or its master could not be found. The following locations were searched:
  
> ~/Views/Person/Person.aspx
  
> ~/Views/Person/Person.ascx
  
> ~/Views/Shared/Person.aspx
  
> ~/Views/Shared/Person.ascx
  
> Beschrijving: Er is een onverwerkte uitzondering opgetreden tijdens het uitvoeren van de huidige webaanvraag. Raadpleeg de stacktracering voor meer informatie over deze fout en de oorsprong ervan in de code.
> 
> Details van uitzondering: System.InvalidOperationException: The view &#8216;Person&#8217; or its master could not be found. The following locations were searched:
  
> ~/Views/Person/Person.aspx
  
> ~/Views/Person/Person.ascx
  
> ~/Views/Shared/Person.aspx
  
> ~/Views/Shared/Person.ascx
> 
> Fout in bron:
> 
> Er is een onverwerkte uitzondering gegenereerd tijdens het uitvoeren van de huidige webaanvraag. Aan de hand van de onderstaande tracering van de uitzonderingsstack kunt u meer informatie verkrijgen over de oorsprong en de locatie van de uitzondering.
> 
> Stacktracering:
> 
> [InvalidOperationException: The view &#8216;Person&#8217; or its master could not be found. The following locations were searched:
  
> ~/Views/Person/Person.aspx
  
> ~/Views/Person/Person.ascx
  
> ~/Views/Shared/Person.aspx
  
> ~/Views/Shared/Person.ascx] 

So we seem to have a choice where we put our view and how to name it.

~/Views/Person/Person.aspx
  
~/Views/Person/Person.ascx
  
~/Views/Shared/Person.aspx
  
~/Views/Shared/Person.ascx

I will just create a new folder in views and call it Person and then a new page called Person.aspx.

```asp
&lt;%@ Page Language="C#" MasterPageFile="~/Views/Shared/Site.Master" Inherits="System.Web.Mvc.ViewPage" %&gt;

&lt;asp:Content ID="indexTitle" ContentPlaceHolderID="TitleContent" runat="server"&gt;
    PersonPage
&lt;/asp:Content&gt;

&lt;asp:Content ID="indexContent" ContentPlaceHolderID="MainContent" runat="server"&gt;
    &lt;h2&gt;Person&lt;/h2&gt;
    &lt;p&gt; FirstName: &lt;%= Html.Encode(ViewData["FirstName"]) %&gt;&lt;br /&gt;
        LastName: &lt;%= Html.Encode(ViewData["LastName"]) %&gt;
    &lt;/p&gt;
&lt;/asp:Content&gt;
```
The important part is this.

```asp
&lt;%= Html.Encode(ViewData["FirstName"]) %&gt;```
Where I say to get the ViewData from the somewhere. If I change my controller like this.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.Mvc;

namespace MvcApplication1.Controllers
{
    [HandleError]
    public class PersonController : Controller
    {
        public ActionResult Person()
        {
            ViewData["FirstName"] = "Christiaan";
            ViewData["LastName"] = "Baes";
            return View();
        }
    }
}
```
I will even get something to show up on that page.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/WebDev/mvcnewproject4.jpg" alt="" title="" width="377" height="278" />
</div>

So all this in less than 5 minutes. Next stop is using some real data.

 [1]: http://www.asp.net/mvc/
 [2]: http://java.sun.com/javaee/javaserverfaces/
 [3]: http://go.microsoft.com/fwlink/?LinkId=144443