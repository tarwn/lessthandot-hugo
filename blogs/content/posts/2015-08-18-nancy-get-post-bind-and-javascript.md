---
title: Nancy, Get, Post, Bind and javascript
author: Christiaan Baes (chrissie1)
type: post
date: 2015-08-18T06:17:12+00:00
ID: 4096
url: /index.php/uncategorized/nancy-get-post-bind-and-javascript/
views:
  - 4447
rp4wp_auto_linked:
  - 1
categories:
  - Uncategorized

---
Something that always seemed to lack a bit of documentation was the Bind method. I know it works with a Post but it also works with a Get. Both are slightly different in how they pass their information to the server. You don&#8217;t see it when you have a form and do a normal submit though.
  
In a RESTfull application you would use a POST to create a new object and a GET for getting information and not changing anything serverside.

Of course there are many other reasons when you should use POST over GET or vice versa. 

[W3Schools][1]

For this we need some code. In the following example I&#8217;m using GET and POST wrong since the POST doesn&#8217;t create anything, but I&#8217;m just making a point. 

Here is a very simple module.

```vbnet
Option Strict Off
Option Explicit Off

Imports Nancy
Imports Nancy.ModelBinding

Namespace Modules
    Public Class ExhibitsModule
        Inherits NancyModule

        Public Sub New()
            [Get]("") = Function(parameters)
                            Return View("index")
                        End Function
            [Get]("/persons") = Function(parameters)
                                    Return MakePerson(Bind(Of Person)())
                                End Function
            [Post]("/persons") = Function(parameters)
                                     Return MakePerson(Bind(Of Person)())
                                 End Function
        End Sub

        Public Function MakePerson(person As Person) As String
            Return person.LastName & " " & person.FirstName
        End Function


        Public Class Person
            Public Property FirstName As String
            Public Property LastName As String
        End Class

    End Class
End Namespace
```
The module has a way to open the view and a way to get and post data.
  
The get and post methods are exactly the same. 

In simple html (a razor view here) we can call both methods in our form by just changing the method. for the rest they are the same.

```html
@Inherits Nancy.ViewEngines.Razor.NancyRazorViewBase(Of String)

@Code
    Layout = Nothing
End Code






    

<div>
  
</div>


```

These days of course nobody uses the plain old submit anymore since it reloads your page, which apparently is very bad.
  
So we can change our code to no longer submit but to react to an onclick event of the buttons. Don&#8217;t forget to make your buttons of type button so they don&#8217;t submit the form anymore.
  
And see the code below with added javascript.

```html
@Inherits Nancy.ViewEngines.Razor.NancyRazorViewBase(Of String)

@Code
    Layout = Nothing
End Code






    

<div>
  <div>
    <ul id="result">
      
    </ul>
  </div>       
      
</div>
    
    


```

I used some jquery magic to make the making of the queryparameters easier. 

In the Post you pass the data in the send method of the XMLHttpRequest in the GET method you add the data as queryparameters to the url.

 [1]: http://www.w3schools.com/tags/ref_httpmethods.asp "W3Schools"