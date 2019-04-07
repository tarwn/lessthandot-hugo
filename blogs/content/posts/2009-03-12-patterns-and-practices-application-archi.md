---
title: Patterns And Practices Application Architecture Guide 2.0, Something Everyone Should Read
author: SQLDenis
type: post
date: 2009-03-12T11:04:46+00:00
ID: 347
excerpt: |
  I was listening to show number 426 on dotnetrocks: Rob Boucher on Application Architecture Guidance! They mentioned the Patterns And Practices Application Architecture Guide 2.0, this guide is available for free on codeplex.
  
  Although it is a Microsof&hellip;
url: /index.php/architect/hardwareinfrastructuredesign/patterns-and-practices-application-archi/
views:
  - 6413
rp4wp_auto_linked:
  - 1
categories:
  - Hardware and Infrastructure Design
tags:
  - architecture
  - best practices
  - cheat sheets
  - data access
  - design
  - design patterns
  - workflow

---
I was listening to show number 426 on dotnetrocks: [Rob Boucher on Application Architecture Guidance!][1] They mentioned the [Patterns And Practices Application Architecture Guide 2.0][2], this guide is available for free on codeplex.

Although it is a Microsoft technology centric guide, there should be chapters for every developer in your group.
  
Here is one example from the book

### Key Design Principles
  


When getting started with your design, bear in mind the key principles that will help you to create architecture that meets “best practices,” minimizes costs and maintenance requirements, and promotes usability and extendibility. The key principles are: 

  * **Separation of concerns**. Break your application into distinct features that overlap in functionality as little as possible.
  * **Single Responsibility Principle**. Each component or a module should be responsible for only a specific feature or functionality.
  * **Principle of least knowledge**.\*\* A component or an object should not know about internal details of other components or objects. Also known as the Law of Demeter\*\* (LoD).
  * **Don’t Repeat Yourself (DRY)**. There should be only one component providing a specific functionality; the functionality should not be duplicated in any other component.
  * **Avoid doing a big design upfront**. If your application requirements are unclear, or if there is a possibility of the design evolving over time, avoid making a large design effort prematurely. This design principle is often abbreviated as BDUF. 
  * **Prefer composition over inheritance**. Wherever possible, use composition over inheritance when reusing functionality because inheritance increases the dependency between parent and child classes, thereby limiting the reuse of child classes.

The nice thing is that each chapter has a resource section at the bottom so that you can dive deep into a specific topic mentioned in the chapter itself. This is great stuff and I recommend that you check out this guide. You also might want to listen to the dotnetrocks podcast about this guide: http://www.dotnetrocks.com/default.aspx?showNum=426

Here is a list of all the chapters, make sure you check out the cheat sheets

### Chapters
  


  * [Introduction][3]
  * [Architecture and Design Solutions At a Glance][4]
  * [Fast Track][5]

#### Part I, Fundamentals
  


  * [Chapter 1 &#8211; Fundamentals of Application Architecture][6] 
  * [Chapter 2 &#8211; .NET Platform Overview][7]
  * [Chapter 3 &#8211; Architecture and Design Guidelines][8]

#### Part II, Design
  


  * [Chapter 4 &#8211; Designing Your Architecture][9]
  * [Chapter 5 &#8211; Deployment Patterns][10]
  * [Chapter 6 &#8211; Architectural Styles][11]
  * [Chapter 7 &#8211; Quality Attributes][12]
  * [Chapter 8 &#8211; Communication Guidelines][13]

#### Part III, Layers
  


  * [Chapter 9 &#8211; Layers and Tiers][14]
  * [Chapter 10 &#8211; Presentation Layer Guidelines][15]
  * [Chapter 11 &#8211; Business Layer Guidelines][16]
  * [Chapter 12 &#8211; Data Access Layer Guidelines][17]
  * [Chapter 13 &#8211; Service Layer Guidelines][18]

#### Part IV, Archetypes
  


  * [Chapter 14 &#8211; Application Archetypes][19]
  * [Chapter 15 &#8211; Web Applications][20]
  * [Chapter 16 &#8211; Rich Internet Applications (RIA)][21]
  * [Chapter 17 &#8211; Rich Client Applications][22]
  * [Chapter 18 &#8211; Services][23]
  * [Chapter 19 &#8211; Mobile Applications][24]
  * [Chapter 20 &#8211; Office Business Applications (OBA)][25]
  * [Chapter 21 &#8211; SharePoint Line-Of-Business (LOB) Applications][26]

#### Appendix
  


  * [Cheat Sheet &#8211; patterns & practices Pattern Catalog][27]
  * [Cheat Sheet &#8211; Presentation Technology Matrix][28]
  * [Cheat Sheet &#8211; Data Access Technology Matrix][29]
  * [Cheat Sheet &#8211; Workflow Technology Matrix][30]
  * [Cheat Sheet &#8211; Integration Technology Matrix][31]

### Errata Page
  


  * [Errata Page][32]

 [1]: http://www.dotnetrocks.com/default.aspx?showNum=426
 [2]: http://www.codeplex.com/AppArchGuide
 [3]: http://apparchguide.codeplex.com/Wiki/View.aspx?title=Introduction%20V2&referringTitle=Home
 [4]: http://apparchguide.codeplex.com/Wiki/View.aspx?title=Architecture%20and%20Design%20Solutions%20At%20a%20Glance&referringTitle=Home
 [5]: http://apparchguide.codeplex.com/Wiki/View.aspx?title=Fast%20Track&referringTitle=Home
 [6]: http://apparchguide.codeplex.com/Wiki/View.aspx?title=Chapter%201%20-%20Architecture%20Fundamentals&referringTitle=Home
 [7]: http://apparchguide.codeplex.com/Wiki/View.aspx?title=.NET%20Platform%20Overview%20V2&referringTitle=Home
 [8]: http://apparchguide.codeplex.com/Wiki/View.aspx?title=Chapter%203%20-%20Architecture%20and%20Design%20Guidelines&referringTitle=Home
 [9]: http://apparchguide.codeplex.com/Wiki/View.aspx?title=Chapter%204%20-%20Designing%20Your%20Architecture&referringTitle=Home
 [10]: http://apparchguide.codeplex.com/Wiki/View.aspx?title=Chapter%205%20-%20Deployment%20Patterns&referringTitle=Home
 [11]: http://apparchguide.codeplex.com/Wiki/View.aspx?title=Chapter%206%20-%20Architectural%20Styles&referringTitle=Home
 [12]: http://apparchguide.codeplex.com/Wiki/View.aspx?title=Chapter%207%20-%20Quality%20Attributes&referringTitle=Home
 [13]: http://apparchguide.codeplex.com/Wiki/View.aspx?title=Chapter%208%20-%20Communication%20Guidelines&referringTitle=Home
 [14]: http://apparchguide.codeplex.com/Wiki/View.aspx?title=Chapter%209%20-%20Layers%20and%20Tiers&referringTitle=Home
 [15]: http://apparchguide.codeplex.com/Wiki/View.aspx?title=Chapter%2010%20-%20Presentation%20Layer%20Guidelines&referringTitle=Home
 [16]: http://apparchguide.codeplex.com/Wiki/View.aspx?title=Chapter%2011%20-%20Business%20Layer%20Guidelines&referringTitle=Home
 [17]: http://apparchguide.codeplex.com/Wiki/View.aspx?title=Chapter%2012%20-%20Data%20Access%20Layer%20Guidelines&referringTitle=Home
 [18]: http://apparchguide.codeplex.com/Wiki/View.aspx?title=Chapter%2013%20-%20Service%20Layer%20Guidelines&referringTitle=Home
 [19]: http://apparchguide.codeplex.com/Wiki/View.aspx?title=Chapter%2014%20-%20Application%20Archetypes&referringTitle=Home
 [20]: http://apparchguide.codeplex.com/Wiki/View.aspx?title=Chapter%2015%20-%20Web%20Applications&referringTitle=Home
 [21]: http://apparchguide.codeplex.com/Wiki/View.aspx?title=Chapter%2016%20-%20Rich%20Internet%20Applications%20%28RIA%29&referringTitle=Home
 [22]: http://apparchguide.codeplex.com/Wiki/View.aspx?title=Chapter%2017%20-%20Rich%20Client%20Applications&referringTitle=Home
 [23]: http://apparchguide.codeplex.com/Wiki/View.aspx?title=Chapter%2018%20-%20Services&referringTitle=Home
 [24]: http://apparchguide.codeplex.com/Wiki/View.aspx?title=Chapter%2019%20-%20Mobile%20Applications&referringTitle=Home
 [25]: http://apparchguide.codeplex.com/Wiki/View.aspx?title=Chapter%2020%20-%20Office%20Business%20Applications%20%28OBA%29&referringTitle=Home
 [26]: http://apparchguide.codeplex.com/Wiki/View.aspx?title=Chapter%2021%20-%20SharePoint%20LOB%20Applications&referringTitle=Home
 [27]: http://apparchguide.codeplex.com/Wiki/View.aspx?title=Pattern%20Catalog%20V2&referringTitle=Home
 [28]: http://apparchguide.codeplex.com/Wiki/View.aspx?title=Cheat%20Sheet%20-%20Presentation%20Technology%20Matrix&referringTitle=Home
 [29]: http://apparchguide.codeplex.com/Wiki/View.aspx?title=Cheat%20Sheet%20-%20Data%20Access%20Technology%20Matrix&referringTitle=Home
 [30]: http://apparchguide.codeplex.com/Wiki/View.aspx?title=Cheat%20Sheet%20-%20Workflow%20Technology%20Matrix&referringTitle=Home
 [31]: http://apparchguide.codeplex.com/Wiki/View.aspx?title=Cheat%20Sheet%20-%20Integration%20Technology%20Matrix&referringTitle=Home
 [32]: http://apparchguide.codeplex.com/Wiki/View.aspx?title=Errata%20Page&referringTitle=Home