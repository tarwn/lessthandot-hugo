---
title: 'Nancy and localization: The better cookie approach'
author: Christiaan Baes (chrissie1)
type: post
date: 2015-02-26T11:15:15+00:00
ID: 3270
url: /index.php/uncategorized/nancy-and-localization-the-better-cookie-approach/
views:
  - 955
categories:
  - Uncategorized

---
## Introduction

In [my previous post][1] I showed you how to keep the culture in a cookie and read from it and set the Context.Culture in each request. So after I wrote that the very handsome Jonathan Channon told me not to because Nancy has this already built in.

## The code

Simple. First you remove the bootstrapper file I had in the previous post. And then you set the CurrentCulture cookie. 

```vbnet
Imports System.Globalization
Imports Nancy

Namespace Modules
    Public Class HomeModule
        Inherits NancyModule

        Public Sub New()
            [Get]("/") = Function(parameters)
                             Return View("Home")
                         End Function
            [Get]("/{locale}") = Function(parameters)
                                     Try
                                         Context.Culture = New CultureInfo(parameters.locale.ToString)
                                     Catch ex As Exception
                                         Context.Culture = New CultureInfo("en-US")
                                     End Try
                                     Return View("Home").WithCookie(New Cookies.NancyCookie("CurrentCulture", Context.Culture.Name))
                                 End Function
        End Sub

    End Class
End Namespace
```
Nancy reads this cookie and will set the culture from that by default. 

You can see how that work in code on the [github repo][2].

## Conclusion

There is always a better way that I don&#8217;t know about.

 [1]: /index.php/uncategorized/nancy-and-localization-the-cookie-approach/
 [2]: https://github.com/NancyFx/Nancy/blob/master/src/Nancy/Conventions/BuiltInCultureConventions.cs#L121