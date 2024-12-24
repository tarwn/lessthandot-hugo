---
title: 'Servicestack CredentialsAuthentication and easyhtpp of course: Part 2'
author: Christiaan Baes (chrissie1)
type: post
date: 2012-10-04T10:06:00+00:00
ID: 1742
excerpt: "Using servicestack to make a list of good and bad children so Santa can easily find what he needs when it's that time of the year again. And making an easyhttp client to go with it."
url: /index.php/desktopdev/mstech/servicestack-credentialsauthentication-and-easyhtpp-of-1/
views:
  - 5411
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

I decided to help Santa by using ServiceStack and Easyhttp. And I described the servicestack in [the first part of this post][1].

## Easyhttp

First of all we need an httpclient.

```vbnet
Private http As HttpClient

    Private Sub InitializeClient()
        If http Is Nothing Then
            http = New HttpClient()
            http.Request.Accept = HttpContentTypes.ApplicationJson
        End If
    End Sub```
Then we need a way to read our data. I wrote a DoGet method for this.

```vbnet
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
    End Function```
Here I catch the httpStatuscode that tells me I&#8217;m unauthorized. So I can tell Santa to log in.

Now I just need a Login method.

```vbnet
Private Sub Authorize()
        Dim response = http.Post("http://localhost:58241/auth", New With {.UserName = "cbaes", .Password = "test1"}, HttpContentTypes.ApplicationJson)
        http.Request.Cookies = response.Cookie
        Console.WriteLine(response.DynamicBody.SessionId)
        Console.WriteLine(response.DynamicBody.UserName)
        Console.WriteLine("")
    End Sub```
Easy right. But watch out for the cookiemonster. I had to tell request to get the cookies from the response otherwise this won&#8217;t work. But Hadi promised to fix this next week.

One more method is for printing our results.

```vbnet
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
    End Sub```
Lastly I also want to logout.

```vbnet
Private Sub LogOut()
        Dim response = http.Get("http://localhost:58241/auth/logout")
        http.Request.Cookies = response.Cookie
    End Sub```
I used a get for that, not sure that is the right way to do that, but it works.

And last but not least we put it all together.

```vbnet
Sub Main()
        Dim response As HttpResponse
        InitializeClient()
        PrintChild(DoGet("http://localhost:58241/GoodOrBad", Nothing))
        Console.WriteLine("")
        Authorize()
        PrintChild(DoGet("http://localhost:58241/GoodOrBad", Nothing))
        Console.WriteLine("")
        LogOut()
        PrintChild(DoGet("http://localhost:58241/GoodOrBad", Nothing))
        Console.WriteLine("")
        Console.ReadLine()
    End Sub```
And here is the result.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/servicestack/ServiceStackSanta5.png?mtime=1349339440"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/servicestack/ServiceStackSanta5.png?mtime=1349339440" width="285" height="251" /></a>
</div>

## Conclusion

Doing a get to do a log is a bit illogical but I&#8217;m probably doing it wrong. For the rest it was pretty painless.

And I seem to be a man of little words but lots of code. Oh well, there is worse things in this world.

In post 3 we will learn more about [the use of roles][2].

 [1]: /index.php/DesktopDev/MSTech/servicestack-credentialsauthentication-and-easyhtpp-of
 [2]: /index.php/DesktopDev/MSTech/servicestack-credentialsauthentication-and-easyhtpp-of-2