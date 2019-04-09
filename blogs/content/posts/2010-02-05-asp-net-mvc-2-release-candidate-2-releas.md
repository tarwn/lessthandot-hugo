---
title: ASP.NET MVC 2 (Release Candidate 2) Released
author: SQLDenis
type: post
date: 2010-02-05T10:48:16+00:00
ID: 694
url: /index.php/webdev/webdesigngraphicsstyling/asp-net-mvc-2-release-candidate-2-releas/
views:
  - 11401
rp4wp_auto_linked:
  - 1
categories:
  - AJAX
  - ASP.NET
  - Javascript
  - Server Programming
  - Web Design, Graphics and Styling
  - XHTML and CSS
tags:
  - asp.net
  - asp.net mvc
  - mvc

---
ASP.NET MVC 2 RC 2 has been released and is available for download.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/WebDev//mvc-logo-landing-page.png" alt="" title="" width="142" height="58" />
</div>

For those that don't know what ASP.NET MVC 2 is. ASP.NET MVC 2 is a framework for developing highly testable and maintainable Web applications by leveraging the Model-View-Controller (MVC) pattern. The framework encourages developers to maintain a clear separation of concerns among the responsibilities of the application – the UI logic using the view, user-input handling using the controller, and the domain logic using the model. ASP.NET MVC applications are easily testable using techniques such as test-driven development (TDD).

The installation package includes templates and tools for Visual Studio 2008 SP 1 to increase productivity when writing ASP.NET MVC applications. For example, the Add View dialog box takes advantage of customizable code generation (T4) templates to generate a view based on a model object. The default project template allows the developer to automatically hook up a unit-test project that is associated with the ASP.NET MVC application.
  
Because the ASP.NET MVC framework is built on ASP.NET 3.5 SP 1, developers can take advantage of existing ASP.NET features like authentication and authorization, profile settings, localization, and so on.

**New Features in RC 2**
  
Default validation system validates entire model
  
The default validation system in ASP.NET MVC 1.0 and in previews of ASP.NET MVC 2 prior to RC 2 validated only model properties that were posted to the server. In ASP.NET MVC 2, the new behavior is that all model properties are validated when the model is validated, regardless of whether a new value was posted. Applications that depend on the ASP.NET MVC 1.0 behavior may require changes

**Other Improvements** 
  
The following changes have been made to existing types and members for the ASP.NET MVC 2 RC 2 release. 

  * The MicrosoftAjax.js script file in new projects has been updated to the version of ASP.NET Ajax that is included in ASP.NET 4. The new script file is compatible with both ASP.NET 3.5 SP1 and ASP.NET 4.
  * Many areas of the framework have had performance improvements.
  * The TempDataDictionary type has a new Peek method that reads values from TempData without removing the values from the dictionary.
  * Templated helpers such as Html.EditorFor and Html.DisplayFor show only simple properties by default. If you need to show complex properties, you can create a custom template to show any set of properties.
  * The Add View context menu in Visual Studio lets you create a view to delete items. The existing List template has a new Delete link for each item in the list.
  * The validation helpers no longer render a default “form0” prefix for the id attribute.
  * Expression-based helpers that render input elements generate correct name attributes when the expression contains an array or collection index. For example, the value of the name attribute rendered by Html.EditorFor(m => m.Orders[i]) for the first order in a list would be Orders[0].
  * The new UrlParameter type allows default values in routes to be removed after URL routing runs. If an incoming route parameter has a value of UrlParameter.Optional, the MvcHandler instance will remove it from RouteData.Values collection before the controller action is executed. The Global.asax file in new projects uses UrlParameter.Optional in the default route definition. This makes it easier to bind to models that have a property named ID, because the default ID route parameter will not conflict with the binding operation.
  * The empty project template includes a small Site.css file that contains styles that are used by validation helpers such as Html.ValidationSummary and Html.ValidationMessage. 
  * T4 template files can use the <#@ output extension=".ext" #> directive to specify the file extension for the generated file.

You can download ASP.NET MVC 2 (Release Candidate 2) here: http://www.microsoft.com/downloads/details.aspx?FamilyID=7aba081a-19b9-44c4-a247-3882c8f749e3&displaylang=en

Make sure to check Scott Guthrie's blog post about ASP.NET MVC 2 RC 2 here: http://weblogs.asp.net/scottgu/archive/2010/02/05/asp-net-mvc-2-release-candidate-2-now-available.aspx