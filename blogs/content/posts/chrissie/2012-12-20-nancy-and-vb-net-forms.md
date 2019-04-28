---
title: 'Nancy and VB.Net: Forms authentication'
author: Christiaan Baes (chrissie1)
type: post
date: 2012-12-20T06:48:00+00:00
ID: 1870
excerpt: Adding Forms authentication to our Nancy project.
url: /index.php/webdev/serverprogramming/nancy-and-vb-net-forms/
views:
  - 7626
categories:
  - ASP.NET
  - Server Programming
tags:
  - nancy
  - vb.net

---
## Introduction

I am making this [little project][1] in Nancy because I want to use it at work next year. I tried out most of the things I will be needing and trying to get a feel of the product. This has of course resulted in some blogposts. And it will save me a lot of time once I get back. 

Yesterday it was time to add some authentication to my pages. I choose to use Form Authentication.

## Setting it up

You will just have to add the Nancy.Authentication.Forms to your project. 

And then the first thing to do is to [alter your bootstrapper][2].

And add this to it.

```vbnet
Protected Overrides Sub ConfigureRequestContainer(container As TinyIoCContainer, context As NancyContext)
        MyBase.ConfigureRequestContainer(container, context)
        container.Register(Of IUserMapper, FakeUserMapper)()
    End Sub

    Protected Overrides Sub RequestStartup(container As TinyIoCContainer, pipelines As IPipelines, context As NancyContext)
        Dim formsAuthConfiguration = New FormsAuthenticationConfiguration With
        {
            .RedirectUrl = "~/login",
            .UserMapper = container.Resolve(Of IUserMapper)()
        }
        FormsAuthentication.Enable(pipelines, formsAuthConfiguration)
    End Sub```
The above tells us that the route to our login page will be /login and that we need a class that implement IUserMapper and that is called FakeUserMapper. So here we go.

```vbnet
Imports Nancy.Authentication.Forms
Imports WebApplication2.Services

Namespace Security
    Public Class FakeUserMapper
        Implements IUserMapper

        Private ReadOnly _userService As UserService

        Public Sub New(userService As UserService)
            _userService = userService
        End Sub


        Public Function GetUserFromIdentifier(ByVal identifier As Guid, ByVal context As Nancy.NancyContext) As Nancy.Security.IUserIdentity Implements IUserMapper.GetUserFromIdentifier
            Dim user = _userService.GetById(identifier)
            Return New AuthenticatedUser() With
            {
                .UserName = user.Name,
                .Claims = user.Claims
            }
        End Function

    End Class
End Namespace```
So I will also need a UserService and an AuthenticatedUser class.

```vbnet
Imports Nancy.Security

Namespace Security

    Public Class AuthenticatedUser
        Implements IUserIdentity

        Public Property UserName() As String Implements IUserIdentity.UserName
        Public Property Claims() As IEnumerable(Of String) Implements IUserIdentity.Claims
    End Class
End Namespace```
```vbnet
Imports Microsoft.VisualBasic.ApplicationServices
Imports WebApplication2.Model

Namespace Services
    Public Class UserService

        Private ReadOnly _users As IList(Of UserModel)

        Public Sub New()
            _users = New List(Of UserModel)
            _users.Add(New UserModel With {.Id = New Guid("00000000000000000000000000000004"), .Name = "Chris1", .Password = "123"})
            _users.Add(New UserModel With {.Id = New Guid("00000000000000000000000000000001"), .Name = "Chris2", .Password = "123"})
            _users.Add(New UserModel With {.Id = New Guid("00000000000000000000000000000002"), .Name = "Chris3", .Password = "123"})
            _users.Add(New UserModel With {.Id = New Guid("00000000000000000000000000000003"), .Name = "Chris4", .Password = "123"})
        End Sub

        Public Function GetUsers() As IList(Of UserModel)
            Return _users
        End Function

        Public Function AuthenticateUser(ByVal username As String, ByVal password As String)
            Dim user = _users.SingleOrDefault(Function(userModel) userModel.Name = username)
            If user IsNot Nothing AndAlso Not user.Password.Equals(password) Then
                Return Nothing
            End If
            Return user
        End Function

        Public Function GetById(ByVal identifier As Guid) As UserModel
            Return _users.SingleOrDefault(Function(userModel) userModel.Id = identifier)
        End Function
    End Class
End Namespace```
<span class="MT_red">Warning! Warning! Warning!</span>

<span class="MT_red">Don&#8217;t use New Guid(&#8220;00000000000000000000000000000000&#8221;) that is not considered to be a Guid. And it will have you scratching your hair for a few hours. If only I had hair.</span>

Now we just need a module to intercept our login route.

```vbnet
Option Strict Off

Imports System.Dynamic
Imports Nancy.Authentication.Forms
Imports Nancy.ModelBinding
Imports Nancy
Imports WebApplication2.Services

Namespace Modules

    Public Class LoginModule
        Inherits NancyModule

        Public Sub New(ByVal userService As UserService)
            MyBase.Get("/login") = Function(parameters)
                                       Return View("login.vbhtml")
                                   End Function
            MyBase.Post("/login") = Function(parameters)
                                        Dim loginParams = Me.Bind(Of LoginParams)()
                                        Dim user = userService.AuthenticateUser(loginParams.Username, loginParams.Password)
                                        If user Is Nothing Then
                                            Return "Your username and password were incorrect please enter a correct one."
                                        End If
                                        Return Me.LoginAndRedirect(user.Id)
                                    End Function
            MyBase.Get("/logout") = Function(parameters)
                                        Return Me.LogoutAndRedirect("~/")
                                    End Function
        End Sub
    End Class

    Public Class LoginParams
        Public Property Username As String
        Public Property Password As String
    End Class
End Namespace```
So we got a get for login to show our login page, we got a post for login so that people can be authenticated and we got a get for logout. The important methods are LoginAndRedirect and LogoutAndRedirect. 

Here is the View that goes with that.

```vbnet
@Code
    ViewBag.Title = "Index page"
    Layout = "Master.vbhtml"
End Code

    &lt;form method="POST"&gt;
        Username &lt;input type="text" name="Username" /&gt;
        &lt;br /&gt;
        Password &lt;input name="Password" type="password" /&gt;
        &lt;br /&gt;
        &lt;input type="submit" value="Login" /&gt;
    &lt;/form&gt;```
So now we have our login process all set up. Now it is time to protect something.

## The protected

I will protect the user information in this one.

Our module is super simple.

```vbnet
Imports WebApplication2.Model
Imports Nancy
Imports Nancy.Security
Imports WebApplication2.Services

Namespace Modules

    Public Class UsersModule
        Inherits NancyModule

        Public Sub New(userService As UserService)
            Me.RequiresAuthentication()
            MyBase.Get("/users") = Function(parameters)
                                       Return View(New UsersModel() With {.Users = userService.GetUsers()})
                                   End Function
            MyBase.Get("/users/{Id}") = Function(parameters)
                                            Dim result As Guid
                                            Dim isInteger = Guid.TryParse(parameters.id, result)
                                            Dim user = userService.GetById(result)
                                            If isInteger AndAlso user IsNot Nothing Then
                                                Return View(user)
                                            Else
                                                Return HttpStatusCode.NotFound
                                            End If
                                        End Function
        End Sub
    End Class
End Namespace```
The one important line is the RequiresAuthetication. And that will make it work. Simple.

Now we just need our views.

But you can find all those on [the github thing][1], together with the models.

## Testing

So now that you got all that working it is time to write our tests&#8230; and from this point forward you can start writing tests first. 

If we take the way we have been testing our modules before we will get a 401. But we needed to test that anyway.

Here is one such test in complete isolation, just for you.

```vbnet
&lt;Test()&gt;
        Public Sub IfPlantWithId2ReturnsWebpagePlantWithId2()
            Dim loggedInBrowserResponse As BrowserResponse
            Dim formsAuthenticationConfiguration = New FormsAuthenticationConfiguration() With
                        {
                            .RedirectUrl = "~/login",
                            .UserMapper = New FakeUserMapper(New UserService())
                        }
            Dim configuration = A.Fake(Of IRazorConfiguration)()
            Dim bootstrapper = New ConfigurableBootstrapper(Sub(config)
                                                                 config.Module(Of UsersModule)()
                                                                 config.Module(Of LoginModule)()
                                                                 config.ViewEngine(New RazorViewEngine(configuration))
                                                                 config.RequestStartup(Sub(x, pipelines, z)
                                                                                           FormsAuthentication.Enable(pipelines, formsAuthenticationConfiguration)
                                                                                       End Sub)
                                                             End Sub)
            loggedInBrowserResponse = New Browser(bootstrapper2).Post("/login", Sub(x)
                                                                                     x.HttpRequest()
                                                                                     x.FormValue("Username", "Chris1")
                                                                                     x.FormValue("Password", "123")
                                                                                 End Sub)
Dim result = loggedInBrowserResponse.Then.Get("/users/00000000-0000-0000-0000-000000000004", Sub(x)
                                                                                                              x.HttpRequest()
                                                                                                          End Sub)
            Assert.AreEqual("00000000-0000-0000-0000-000000000004", result.BodyAsXml.Descendants("td")(1).Value)
        End Sub```
As you can see I had to add FormsAuth to my custom bootstrapper.
  
I then did a post with some correct credentials.
  
And then we do a get of a user. And checked if that data was in the response.
  
Also make sure you add the LoginModule to your bootstrapper
  
Simples, once you know how.

## Conclusion

This seems like a lot of code, but once you have it set up it&#8217;s pretty much ok.

Next post I will explain how to get the login/logout link to appear on each page. Next post after the next post that is. Because the next post is post 500 and that is special like me.

 [1]: https://github.com/chrissie1/NancyVB
 [2]: /index.php/All/?p=1967