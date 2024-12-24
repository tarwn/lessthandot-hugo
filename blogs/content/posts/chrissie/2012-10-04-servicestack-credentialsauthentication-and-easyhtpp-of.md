---
title: 'Servicestack CredentialsAuthentication and easyhtpp of course: Part 1'
author: Christiaan Baes (chrissie1)
type: post
date: 2012-10-04T05:51:00+00:00
ID: 1741
excerpt: "Using servicestack to make a list of good and bad children so Santa can easily find what he needs when it's that time of the year again. And making an easyhttp client to go with it."
url: /index.php/desktopdev/mstech/servicestack-credentialsauthentication-and-easyhtpp-of/
views:
  - 9483
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - authentication
  - easyhttp
  - servicestack

---
## Introduction

So this week I wanted to setup a webservice that also included authentication. As one usually does this time of the year, Santa needs his data to be protected so that the children can&#8217;t see which gifts they will receive. 

I decided to help Santa by using [ServiceStack][1] and [Easyhttp][2]. 

## ServiceStack

Lets start by creating our server first. For this I make a new Empty ASP.Net project. And I change the web.config to this.

```xml
&lt;?xml version="1.0"?&gt;

&lt;!--
  For more information on how to configure your ASP.NET application, please visit
  &lt;a href="http://go.microsoft.com/fwlink/?LinkId=169433"&gt;
http://go.microsoft.com/fwlink/?LinkId=169433&lt;/a&gt;
  --&gt;

&lt;configuration&gt;
  &lt;system.web&gt;
    &lt;compilation debug="true" strict="false" explicit="true" targetFramework="4.0" /&gt;
  &lt;/system.web&gt;
  &lt;system.web&gt;
    &lt;httpHandlers&gt;
      &lt;add path="*" type="ServiceStack.WebHost.Endpoints.ServiceStackHttpHandlerFactory, ServiceStack" verb="*"/&gt;
    &lt;/httpHandlers&gt;
  &lt;/system.web&gt;

  &lt;!-- Required for IIS 7.0 --&gt;
  &lt;system.webServer&gt;
    &lt;validation validateIntegratedModeConfiguration="false" /&gt;
    &lt;handlers&gt;
      &lt;add path="*" name="ServiceStack.Factory" type="ServiceStack.WebHost.Endpoints.ServiceStackHttpHandlerFactory, ServiceStack" verb="*" preCondition="integratedMode" resourceType="Unspecified" allowPathInfo="true" /&gt;
    &lt;/handlers&gt;
  &lt;/system.webServer&gt;
&lt;/configuration&gt;```
Then I create Three folders. Request, Response and Service.

The Request Is one Class. With one property. So that the user (Santa and his elf&#8217;s) can select if he wants a list of all the Good Boys and Girls or all the bad ones.

```vbnet
Namespace Request
    Public Class GoodOrBadRequest
        Public Property Good As Boolean?
    End Class
End Namespace```
And the Response will be this.

```vbnet
Namespace DefaultNamespace.Response
    Public Class Children
        Public Property LastName As String
        Public Property Good As Boolean?
    End Class
End Namespace```
And we also need a Service Class.

```vbnet
Imports SantaGoodAndBad.Response
Imports SantaGoodAndBad.Request
Imports ServiceStack.ServiceInterface

Namespace DefaultNamespace.Service
    Public Class GoodOrBadService
        Inherits RestServiceBase(Of GoodOrBadRequest)
        
        Public Overrides Function OnGet(request As GoodOrBadRequest) As Object
            If request.Good.HasValue Then
                Return MakeChildren.Where(Function(child) child.Good.Value = request.Good.Value).ToList
            Else
                Return MakeChildren()
            End If
        End Function

        Private Function MakeChildren() As IList(Of Child)
            Dim children As New List(Of Child)
            children.Add(New Child With {.LastName = "Baes", .Good = True})
            children.Add(New Child With {.LastName = "Hariri", .Good = False})
            children.Add(New Child With {.LastName = "Hanselman", .Good = True})
            children.Add(New Child With {.LastName = "Krueger", .Good = False})
            Return children
        End Function

    End Class
End Namespace```
And then it is time to create a new AppHost file.
  
And now this is where it gets interesting.

```vbnet
Imports ServiceStack.CacheAccess.Providers
Imports ServiceStack.CacheAccess
Imports ServiceStack.ServiceInterface.Auth
Imports SantaGoodAndBad.Request
Imports SantaGoodAndBad.DefaultNamespace.Service
Imports ServiceStack.ServiceInterface
Imports ServiceStack.WebHost.Endpoints

Public Class AppHost
    Inherits AppHostHttpListenerBase

    Public Sub New()
        MyBase.New("Good or bad", GetType(GoodOrBadService).Assembly)
    End Sub

    Public Overrides Sub Configure(container As Funq.Container)
        Routes.Add(Of GoodOrBadRequest)("/GoodOrBad") _
            .Add(Of GoodOrBadRequest)("/GoodOrBad/{Good}")

        Plugins.Add(New AuthFeature(Function() New AuthUserSession(), New AuthProvider() {New CredentialsAuthProvider()}))
        container.Register(Of ICacheClient)(New MemoryCacheClient())
        Dim userRep = New InMemoryAuthRepository()
        container.Register(Of IUserAuthRepository)(userRep)
        Dim hash As String
        Dim salt As String
        Dim s = New SaltedHash()
        s.GetHashAndSaltString("test", hash, salt)
        userRep.CreateUserAuth(New UserAuth With {.Id = 1, .DisplayName = "DisplayName", .Email = "as@if.com", .UserName = "cbaes", .FirstName = "FirstName", .LastName = "LastName", .PasswordHash = hash, .Salt = salt}, "test1")
    End Sub

End Class```
As you can see above. I create 2 routes. And then I add the auth plugin. And one user to authenticate against. And that&#8217;s it.

Now to change our Global.asax.

```vbnet
Imports System.Web.SessionState

Public Class Global_asax
    Inherits System.Web.HttpApplication

    Sub Application_Start(ByVal sender As Object, ByVal e As EventArgs)
        Dim apphost = New AppHost()
        apphost.Init()
    End Sub

End Class```
And now we can view the above in a browser.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/servicestack/ServiceStackSanta1.png?mtime=1349336274"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/servicestack/ServiceStackSanta1.png?mtime=1349336274" width="684" height="209" /></a>
</div>

Now if I go to the GoodOrBad page. I can still see my children.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/servicestack/ServiceStackSanta2.png?mtime=1349336422"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/servicestack/ServiceStackSanta2.png?mtime=1349336422" width="551" height="264" /></a>
</div>

And that was not the point. So what did I forget. 

I forgot to tell ServiceStack which requests to authenticate.

I can do this on the service or on the request object. I will do it on the request object.

```vbnet
Imports ServiceStack.ServiceInterface

Namespace Request
    &lt;Authenticate&gt;
    Public Class GoodOrBadRequest
        Public Property Good As Boolean?
    End Class
End Namespace```
And now when I try to go to GoodOrBad I get this.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/servicestack/ServiceStackSanta3.png?mtime=1349336694"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/servicestack/ServiceStackSanta3.png?mtime=1349336694" width="594" height="547" /></a>
</div>

So I need to login.

You can login by doing this auth?username=cbaes&password=test1

And then you get the following response.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/servicestack/ServiceStackSanta4.png?mtime=1349336831"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/servicestack/ServiceStackSanta4.png?mtime=1349336831" width="468" height="252" /></a>
</div>

And now you can see your data again.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/servicestack/ServiceStackSanta2.png?mtime=1349336422"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/servicestack/ServiceStackSanta2.png?mtime=1349336422" width="551" height="264" /></a>
</div>

And to log back out you can use this /auth/logout

## Easyhttp

This post was getting to long so I will discuss that in [my next post][3]. And in post 3 there is [more about roles][4].

## Conclusion

Authentication is very easy to setup in ServiceStack.

 [1]: https://github.com/ServiceStack/
 [2]: https://github.com/hhariri/EasyHttp
 [3]: /index.php/DesktopDev/MSTech/servicestack-credentialsauthentication-and-easyhtpp-of-1
 [4]: /index.php/DesktopDev/MSTech/servicestack-credentialsauthentication-and-easyhtpp-of-2