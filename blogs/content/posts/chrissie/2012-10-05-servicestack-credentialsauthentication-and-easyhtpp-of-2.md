---
title: 'Servicestack CredentialsAuthentication and easyhtpp of course: Part 3'
author: Christiaan Baes (chrissie1)
type: post
date: 2012-10-05T04:45:00+00:00
ID: 1743
excerpt: In this post I will show you how we give Santa the power to add children to his list.
url: /index.php/desktopdev/mstech/servicestack-credentialsauthentication-and-easyhtpp-of-2/
views:
  - 7439
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

In the first part I showed you [how to configure ServiceStack][1] and in the second part I showed you [how to read from the ServiceStack service with Easyhttp][2]. And now it is time to give Santa the opportunity to add some children.

## ServiceStack

I want to use the same request for my Post as I did for my Get. This means I will need to add LastName to the request.

```vbnet
Imports ServiceStack.ServiceInterface

Namespace Request
    &lt;Authenticate&gt;
    Public Class GoodOrBadRequest
        Public Property Good As Boolean?
        Public Property LastName As String
    End Class
End Namespace```
And then we need to add the Post implementation to our service.

```vbnet
Imports SantaGoodAndBad.Response
Imports SantaGoodAndBad.Request
Imports ServiceStack.ServiceInterface

Namespace DefaultNamespace.Service
    Public Class GoodOrBadService
        Inherits RestServiceBase(Of GoodOrBadRequest)

        Dim children As List(Of Child)

        Public Sub New()
            MakeChildren()
        End Sub

        Public Overrides Function OnPost(request As GoodOrBadRequest) As Object
            children.Add(New Child With {.LastName = request.LastName, .Good = request.Good})
            Return children
        End Function

        Public Overrides Function OnGet(request As GoodOrBadRequest) As Object
            If request.Good.HasValue Then
                Return children.Where(Function(child) child.Good.Value = request.Good.Value).ToList
            Else
                Return children
            End If
        End Function

        Private Function MakeChildren() As IList(Of Child)
            children = New List(Of Child)
            children.Add(New Child With {.LastName = "Baes", .Good = True})
            children.Add(New Child With {.LastName = "Hariri", .Good = False})
            children.Add(New Child With {.LastName = "Hanselman", .Good = True})
            children.Add(New Child With {.LastName = "Krueger", .Good = False})
            Return children
        End Function

    End Class
End Namespace```
The above works just fine, trust me.

However, the client changed their minds, they only want Santa to be able to read and add children to the list and the elfs can read the list but not add children to the list.

For this to work I have to create an elf account with a role of elfs and a santa with roles of santa and elfs. We do this in the apphost for now.

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
        userRep.CreateUserAuth(New UserAuth With {.Id = 1, .DisplayName = "Santa Claus", .Email = "Santa@Claus.com", .UserName = "santa", .FirstName = "Kris", .LastName = "Kringle", .PasswordHash = hash, .Salt = salt, .Roles = New List(Of String) From {"Santa", "Elfs"}}, "jingle")
        userRep.CreateUserAuth(New UserAuth With {.Id = 2, .DisplayName = "Elf 1", .Email = "Elf1@Claus.com", .UserName = "elf1", .FirstName = "Elf", .LastName = "One", .PasswordHash = hash, .Salt = salt, .Roles = New List(Of String) From {"Elfs"}}, "bells")
    End Sub

End Class```
And we need to tell our request which roles are allowed to do what.

```vbnet
Imports ServiceStack.ServiceInterface

Namespace Request
    &lt;Authenticate&gt;
    &lt;RequiredRole(ApplyTo.Get, "Elfs")&gt;
    &lt;RequiredRole(ApplyTo.Post, "Santa")&gt;
    Public Class GoodOrBadRequest
        Public Property Good As Boolean?
        Public Property LastName As String
    End Class
End Namespace```
Yep that was pretty simple.

Now to test this.

## Easyhttp

I added a method to do the post and then read the results and I added another Authorize for easy testing. Here is the complete copy pasteable code

```vbnet
Option Strict Off

Imports EasyHttp.Http

Module Module1

    Sub Main()
        Dim response As HttpResponse
        InitializeClient()
        PrintChild(DoGet("http://localhost:58241/GoodOrBad", Nothing))
        Console.WriteLine("")
        AuthorizeSanta()
        PrintChild(DoGet("http://localhost:58241/GoodOrBad", Nothing))
        Console.WriteLine("")
        PrintChild(DoPost("http://localhost:58241/GoodOrBad", New With {.LastName = "Stack", .Good = False}))
        Console.WriteLine("")
        LogOut()
        AuthorizeElf1()
        PrintChild(DoGet("http://localhost:58241/GoodOrBad", Nothing))
        Console.WriteLine("")
        PrintChild(DoPost("http://localhost:58241/GoodOrBad", New With {.LastName = "Hamilton", .Good = False}))
        Console.WriteLine("")
        LogOut()
        PrintChild(DoGet("http://localhost:58241/GoodOrBad", Nothing))
        Console.WriteLine("")
        Console.ReadLine()
    End Sub

    Private Sub PrintChild(ByVal children As Object)
        If children IsNot Nothing Then
            For Each child In children
                If child.Good = False Then
                    Console.ForegroundColor = ConsoleColor.Red
                Else
                    Console.ForegroundColor = ConsoleColor.Green
                End If
                Console.WriteLine(child.LastName)
            Next
        End If
        Console.ForegroundColor = ConsoleColor.White
    End Sub

    Private Function DoGet(ByVal url As String, ByVal parameters As Object) As Object
        Dim response As HttpResponse = Nothing
        If parameters IsNot Nothing Then
            response = http.Get(url, parameters)
        Else
            response = http.Get(url)
        End If
        If response.StatusCode = Net.HttpStatusCode.Unauthorized Then
            Console.WriteLine("Unauthorized")
            response = Nothing
        End If
        If response IsNot Nothing Then
            Return response.DynamicBody
        Else
            Return Nothing
        End If
    End Function

    Private Function DoPost(ByVal url As String, ByVal parameters As Object) As Object
        Dim response As HttpResponse = Nothing
        If parameters IsNot Nothing Then
            response = http.Post(url, parameters, HttpContentTypes.ApplicationJson)
        Else
            response = Nothing
        End If
        If response.StatusCode = Net.HttpStatusCode.Unauthorized Then
            Console.WriteLine("Unauthorized")
            response = Nothing
        End If
        If response IsNot Nothing Then
            Return response.DynamicBody
        Else
            Return Nothing
        End If
    End Function

    Private Sub AuthorizeSanta()
        Dim response = http.Post("http://localhost:58241/auth", New With {.UserName = "santa", .Password = "jingle"}, HttpContentTypes.ApplicationJson)
        http.Request.Cookies = response.Cookie
        Console.WriteLine(response.DynamicBody.SessionId)
        Console.WriteLine(response.DynamicBody.UserName)
        Console.WriteLine("")
    End Sub

    Private Sub AuthorizeElf1()
        Dim response = http.Post("http://localhost:58241/auth", New With {.UserName = "elf1", .Password = "bells"}, HttpContentTypes.ApplicationJson)
        http.Request.Cookies = response.Cookie
        Console.WriteLine(response.DynamicBody.SessionId)
        Console.WriteLine(response.DynamicBody.UserName)
        Console.WriteLine("")
    End Sub

    Private Sub LogOut()
        Dim response = http.Get("http://localhost:58241/auth/logout")
        http.Request.Cookies = response.Cookie
    End Sub

    Private http As HttpClient

    Private Sub InitializeClient()
        If http Is Nothing Then
            http = New HttpClient()
            http.Request.Accept = HttpContentTypes.ApplicationJson
        End If
    End Sub

End Module
```
And here is the result. 

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/servicestack/ServiceStackSanta6.png?mtime=1349419477"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/servicestack/ServiceStackSanta6.png?mtime=1349419477" width="261" height="419" /></a>
</div>

Which is just as we expected.

## Conclusion

It works but not as I intended. I did not plan on putting santa in the elfs role. I wanted him to be in the sanat role only and then give requiredrole to elfs and santa but that didn&#8217;t work. My mind probably needs to get around this.

 [1]: /index.php/DesktopDev/MSTech/servicestack-credentialsauthentication-and-easyhtpp-of
 [2]: /index.php/DesktopDev/MSTech/servicestack-credentialsauthentication-and-easyhtpp-of-1