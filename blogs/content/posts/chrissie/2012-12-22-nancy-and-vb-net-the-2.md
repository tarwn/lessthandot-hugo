---
title: 'Nancy and VB.Net: the login/logout thing'
author: Christiaan Baes (chrissie1)
type: post
date: 2012-12-22T07:18:00+00:00
ID: 1878
excerpt: How to make a login/logout link on your webpages in nancy with the razorviewengine.
url: /index.php/webdev/serverprogramming/nancy-and-vb-net-the-2/
views:
  - 6379
categories:
  - ASP.NET
  - Server Programming
tags:
  - nancy
  - vb.net

---
## Introduction

Having set up [forms authentication][1] for my little project (which is still on github) I found the need to put a login/logout link/thing on my viewpages.
  
Since I could not immediately find how to this I asked on the <a href="<razor disableAutoIncludeModelNamespace="false"> <assemblies> <add assembly="System, Version=2.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089"> <add assembly="Nancy"> </add></add></assemblies> <namespaces>> <add namespace="Nancy.ViewEngines"> </add></namespaces> e</a> and got a swift answer. That answer helped me along, but we still hit a little bump in the road. Here is my journey.

## The view

In essence it is very simple to make the login/logout link since you are able to use the Nancy context in your view.

All you need is this. 

```xml
@If Url.RenderContext.Context.CurrentUser Is Nothing Then 
        @&lt;a href="/login"&gt;Login&lt;/a&gt;
    Else
        @Url.RenderContext.Context.CurrentUser.UserName  
        @&lt;a href="/logout"&gt;Logout&lt;/a&gt;
    End If
    ```
When a user is not logged in (sometimes also known as logged out) the login link will appear which will guide you to the loginpage. Once the user is logged in we will show his username and the logout link so he can press that and be amazed.

The VB razor syntax was kind of hard to figure out but nothing a little trail and error won&#8217;t fix. 

And then you go and try this and notice the nice errormessage you will get.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/nancy/nancy12.png?mtime=1356166006"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/nancy/nancy12.png?mtime=1356166006" width="510" height="271" /></a>
</div>

With this in the details view.

> Error Details
> 
> Error compiling template: Views/Master.vbhtml
> 
> Errors:
  
> [BC30652] Line: 8 Column: 0 &#8211; Reference required to assembly &#8216;Nancy, Version=0.14.1.0, Culture=neutral, PublicKeyToken=null&#8217; containing the type &#8216;Nancy.ViewEngines.IRenderContext&#8217;. Add one to your project. (show)
  
> [BC30652] Line: 11 Column: 0 &#8211; Reference required to assembly &#8216;Nancy, Version=0.14.1.0, Culture=neutral, PublicKeyToken=null&#8217; containing the type &#8216;Nancy.ViewEngines.IRenderContext&#8217;. Add one to your project. (show)
> 
> Details:
  
> 
  
> 
  
> 
  
> </p> 
> 
> <div id="menu">
>   <span class="MT_red">@If Url.RenderContext.Context.CurrentUser Is Nothing Then</span><br /> @<a href="/login">Login</a><br /> Else<br /> @Url.RenderContext.Context.CurrentUser.UserName<br /> @<a href="/logout">Logout</a><br /> End If<br /> @If IsSectionDefined(&#8220;menu&#8221;) Then<br /> @<a href="/">Index</a><br /> @If IsSectionDefined(&#8220;menuitems&#8221;) Then<br /> @RenderSection(&#8220;menuitems&#8221;)<br /> End if<br /> End If
> </div>
> 
> @RenderBody()
  
> </body>
  
> </html></blockquote> 
> 
> So we need to reference Nancy. But how?
> 
> By adding some xml to our web.config of course.
> 
> ```xml
&lt;configSections&gt;
>     &lt;section name="razor" type="Nancy.ViewEngines.Razor.RazorConfigurationSection, Nancy.ViewEngines.Razor" /&gt;
>   &lt;/configSections&gt;
>   &lt;razor disableAutoIncludeModelNamespace="false"&gt;
>     &lt;assemblies&gt;
>       &lt;add assembly="Nancy" /&gt;
>     &lt;/assemblies&gt;
>     &lt;namespaces&gt;
>       &lt;add namespace="Nancy.ViewEngines" /&gt;
>     &lt;/namespaces&gt;
>   &lt;/razor&gt;```
> And now we get our nice login link.
> 
> <div class="image_block">
>   <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/nancy/nancy13.png?mtime=1356166322"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/nancy/nancy13.png?mtime=1356166322" width="358" height="162" /></a>
> </div>
> 
> Or of course our nice logout link and username when we are logged in.
> 
> <div class="image_block">
>   <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/nancy/nancy14.png?mtime=1356166446"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/nancy/nancy14.png?mtime=1356166446" width="169" height="150" /></a>
> </div>
> 
> <span class="MT_red">Don&#8217;t forget that if you have a test project to add an app.config file and add those same lines as above, else your tests will fail.<br /> </span> 
> 
> Alas this is not enough. Our client doesn&#8217;t want us to show the username of the logged in user but his or hers real name.
> 
> But currentuser only returns us the usernmae. And why is that?
  
> Because currentuser is of type IUserIdentity and that only has two properties, namely: username and claims. And we used IUserIdentity when we made our AuthenticatedUser Class. So nancy is really returning us an AuthenticatedUser object and we can add stuff to that. Like the realname of the user.
> 
> Like this.
> 
> ```vbnet
Imports Nancy.Security
> 
> Namespace Security
> 
>     Public Class AuthenticatedUser
>         Implements IUserIdentity
> 
>         Public Property UserName() As String Implements IUserIdentity.UserName
>         Public Property Claims() As IEnumerable(Of String) Implements IUserIdentity.Claims
>         Public Property RealName As String
>     End Class
> End Namespace```
> Of course we now also have to update our userservice, usermodel, and the two user views to account for this added property.You can sse those changes in the code on github BTW.
> 
> And now we can adapt our loginscript to show the realname instead of the username.
> 
> ```xml
@If Url.RenderContext.Context.CurrentUser Is Nothing Then 
>         @&lt;a href="/login"&gt;Login&lt;/a&gt;
>     Else
>         @CType(Url.RenderContext.Context.CurrentUser, AuthenticatedUser).RealName  
>         @&lt;a href="/logout"&gt;Logout&lt;/a&gt;
>     End If
>     ```
> I just cast currentuser to the type Authenticateduser and use it&#8217;s realname.
> 
> Of course this means I also need to add my assembly to the list of assemblies razor needs and add the namespace.
> 
> Something like this.
> 
> ```xml
&lt;razor disableAutoIncludeModelNamespace="false"&gt;
>     &lt;assemblies&gt;
>       &lt;add assembly="Nancy" /&gt;
>       &lt;add assembly="NancyDemo.VB" /&gt;
>     &lt;/assemblies&gt;
>     &lt;namespaces&gt;
>       &lt;add namespace="Nancy.ViewEngines" /&gt;
>       &lt;add namespace="NancyDemo.VB.Security" /&gt;
>     &lt;/namespaces&gt;
>   &lt;/razor&gt;```
> And now our view looks like this.
> 
> <div class="image_block">
>   <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/nancy/nancy15.png?mtime=1356167873"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/nancy/nancy15.png?mtime=1356167873" width="262" height="157" /></a>
> </div>
> 
> And the sky is the limit again.
> 
> Have fun.

 [1]: /index.php/All/?p=1979