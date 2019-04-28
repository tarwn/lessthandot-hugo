---
title: 'Me and ASP.NET MVC: Less magic strings.'
author: Christiaan Baes (chrissie1)
type: post
date: 2009-03-19T11:24:34+00:00
ID: 352
url: /index.php/webdev/webdesigngraphicsstyling/me-and-asp-net-mvc-less-magic-strings/
views:
  - 3284
categories:
  - Web Design, Graphics and Styling
tags:
  - asp.net mvc
  - 'c#'

---
I worked a little further on my first ASP.Net spike code. I wanted to have a Person that comes frome a repo to show on the screen with less magic string. First I read a bit in [this][1]. But I get bored pretty quickly with reading things so I got back to experimenting. This is the result.

First I create my trusted Person Class.

```csharp
namespace MvcApplication1.Models
{
    public class Person
    {
        private string lastName;
        private string firstName;

        public Person(string LastName, string FirstName)
        {
            lastName = LastName;
            firstName = FirstName;
        }

        public string FirstName { get { return firstName; } set { firstName = value; } }
        public string LastName { get { return lastName; } set { lastName = value; } }
    }
}```
Then I needed a repository to return somethig. One that has this as an interface.

```csharp
namespace MvcApplication1.Dal.Interfaces
{
    public interface IPerson
    {
        Models.Person FindPerson(int index);
    }
}```
And this is one possible implementation.

```csharp
using MvcApplication1.Dal.Interfaces;

namespace MvcApplication1.Dal.Fake
{
    public class Person:IPerson
    {
        public Models.Person FindPerson(int index)
        {
            return new Models.Person("Baes","Christiaan");
        }
    }
}```
And then I needed to inject this in my controller. But injecting it via the construcor is a bit more difficult than I thought so I will just create a new one for now in the constructor (next time I will inject). 

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.Mvc;
using MvcContrib;

namespace MvcApplication1.Controllers
{
    [HandleError]
    public class PersonController : Controller
    {
        private Dal.Interfaces.IPerson personRepository;

        public PersonController()
        {
            personRepository = new Dal.Fake.Person();
        }

        public ActionResult Person()
        {
            var person = personRepository.FindPerson(1);
            return View(person);
        }
    }
}
```
So I got a fakerepository and I get a person from that. Then I inject that persin into the View. Because I don&#8217;t specify a specific View it will just route it to Person, which is fine for now. And no more mahic strings in the controller. Now lets look at the view (.aspx).

```asp
&lt;%@ Page Title="" Language="C#" MasterPageFile="~/Views/Shared/Site.Master" Inherits="System.Web.Mvc.ViewPage&lt;MvcApplication1.Models.Person&gt;" %&gt;

&lt;asp:Content ID="Content1" ContentPlaceHolderID="TitleContent" runat="server"&gt;
	Person
&lt;/asp:Content&gt;

&lt;asp:Content ID="Content2" ContentPlaceHolderID="MainContent" runat="server"&gt;

    &lt;h2&gt;Person&lt;/h2&gt;
    &lt;p&gt; FirstName: &lt;%= Html.Encode(Model.FirstName)%&gt;&lt;br /&gt;
        LastName: &lt;%= Html.Encode(Model.LastName)%&gt;
    &lt;/p&gt;
&lt;/asp:Content&gt;```
This bit <code class="codespan">Inherits="System.Web.Mvc.ViewPage&lt;MvcApplication1.Models.Person&gt;"</code> makes it so that the Model bit of ViewPage is now strongly typed of type Models.Person. Which means that I can now do Html.Encode(Model.FirstName) so no more ViewData[&#8220;FirstName&#8221;] this will make refactoring a lot more easy, great. 

The result is the same as before. But a little more flexible. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/WebDev/mvcnewproject4.jpg" alt="" title="" width="377" height="278" />
</div>

I can now easily add an attribute to Person.

Lets try that. I will add an attribute City.

```csharp
namespace MvcApplication1.Models
{
    public class Person
    {
        private string lastName;
        private string firstName;
        private string city;

        public Person(string LastName, string FirstName, string City )
        {
            lastName = LastName;
            firstName = FirstName;
            city = City;
        }

        public string FirstName { get { return firstName; } set { firstName = value; } }
        public string LastName { get { return lastName; } set { lastName = value; } }
        public string City { get { return city; } set { city = value; } }
    }
}```
I have to change the fakerepo to add a city, of course. Because I changed the constructor I got a compile error so I know what&#8217;s wrong and where (less bugs).

```csharp
using MvcApplication1.Dal.Interfaces;

namespace MvcApplication1.Dal.Fake
{
    public class Person:IPerson
    {
        public Models.Person FindPerson(int index)
        {
            return new Models.Person("Baes","Christiaan","Moerbeke-Waas");
        }
    }
}```
No need to change the controller.

Just a little change to the view.

```asp
&lt;%@ Page Title="" Language="C#" MasterPageFile="~/Views/Shared/Site.Master" Inherits="System.Web.Mvc.ViewPage&lt;MvcApplication1.Models.Person&gt;" %&gt;

&lt;asp:Content ID="Content1" ContentPlaceHolderID="TitleContent" runat="server"&gt;
	Person
&lt;/asp:Content&gt;

&lt;asp:Content ID="Content2" ContentPlaceHolderID="MainContent" runat="server"&gt;

    &lt;h2&gt;Person&lt;/h2&gt;
    &lt;p&gt; FirstName: &lt;%= Html.Encode(Model.FirstName)%&gt;&lt;br /&gt;
        LastName: &lt;%= Html.Encode(Model.LastName)%&gt;&lt;br /&gt;
        City: &lt;%= Html.Encode(Model.City)%&gt;
    &lt;/p&gt;
&lt;/asp:Content&gt;
```
With this as the result.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/WebDev/mvcnewproject5.jpg" alt="" title="" width="306" height="242" />
</div>

Warning: I&#8217;m just learning, I&#8217;m not saying what I have here is good practice. Next tie I will start unittesting.

 [1]: http://aspnetmvcbook.s3.amazonaws.com/aspnetmvc-nerdinner_v1.pdf