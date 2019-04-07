---
title: Nancy, Get, Post, Bind and javascript
author: Christiaan Baes (chrissie1)
type: post
date: 2015-08-18T06:17:12+00:00
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

<pre>Option Strict Off
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
End Namespace</pre>

The module has a way to open the view and a way to get and post data.
  
The get and post methods are exactly the same. 

In simple html (a razor view here) we can call both methods in our form by just changing the method. for the rest they are the same.

<pre>@Inherits Nancy.ViewEngines.Razor.NancyRazorViewBase(Of String)

@Code
    Layout = Nothing
End Code

<!DOCTYPE html&gt;

<html&gt;
<head&gt;
    <title&gt;</title&gt;
    

</head&gt;
<body&gt;
    <div&gt;
        <form action="/persons" method="GET"&gt;
            <input type="text" name="FirstName" value="testfirstnameget" /&gt;
            <input type="text" name="LastName" value="testlastnameget" /&gt;
            <button&gt;Submit</button&gt;
        </form&gt;
        <form action="/persons" method="POST"&gt;
            <input type="text" name="FirstName" value="testfirstnamepost" /&gt;
            <input type="text" name="LastName" value="testlastnamepost" /&gt;
            <button&gt;Submit</button&gt;
        </form&gt;   
    </div&gt;
</body&gt;
</html&gt;</pre>

These days of course nobody uses the plain old submit anymore since it reloads your page, which apparently is very bad.
  
So we can change our code to no longer submit but to react to an onclick event of the buttons. Don&#8217;t forget to make your buttons of type button so they don&#8217;t submit the form anymore.
  
And see the code below with added javascript.

<pre>@Inherits Nancy.ViewEngines.Razor.NancyRazorViewBase(Of String)

@Code
    Layout = Nothing
End Code

<!DOCTYPE html&gt;

<html&gt;
<head&gt;
    <title&gt;</title&gt;
    

</head&gt;
<body&gt;
    <div&gt;
        <form&gt;
            <input id="firstnameget" type="text" name="FirstName" value="testfirstnamegetjs" /&gt;
            <input id="lastnameget" type="text" name="LastName" value="testlastnamegetjs" /&gt;
            <button type="button" onclick="getjs()"&gt;Submit</button&gt;
        </form&gt;
        <form&gt;
            <input id="firstnamepost" type="text" name="FirstName" value="testfirstnamepostjs" /&gt;
            <input id="lastnamepost" type="text" name="LastName" value="testlastnamepostjs" /&gt;
            <button type="button" onclick="postjs()"&gt;Submit</button&gt;
        </form&gt; 
        <div&gt;<ul id="result"&gt;</ul&gt;</div&gt;       
    </div&gt;
    <script src="@Url.Content("~/Content/Scripts/jquery-2.1.4.min.js")" type="text/javascript"&gt;</script&gt;
    <script type="text/javascript"&gt;
        function postjs() {
            var xhr = new XMLHttpRequest();
            var data = { FirstName: $("#firstnamepost").val(), LastName: $("#lastnamepost").val() };
            xhr.open("POST", "/persons", true);
            xhr.setRequestHeader('Content-Type', 'application/json; charset=UTF-8');
            xhr.send(JSON.stringify(data));
            xhr.onreadystatechange = function () {
                if (xhr.readyState == 4 && xhr.status == 200) {
                    $("#result").append("<li&gt;" + xhr.responseText + "</li&gt;");
                };
            }
        }
        function getjs() {
            var xhr = new XMLHttpRequest();
            var data = {FirstName: $("#firstnameget").val(),LastName: $("#lastnameget").val()};
            xhr.open("GET", "/persons?" + $.param(data) , true);
            xhr.setRequestHeader('Content-Type', 'application/json; charset=UTF-8');
            xhr.send();
            xhr.onreadystatechange = function () {
                if (xhr.readyState == 4 && xhr.status == 200) {
                    $("#result").append("<li&gt;" + xhr.responseText + "</li&gt;");
                };
            }
        }
    </script&gt;
</body&gt;
</html&gt;</pre>

I used some jquery magic to make the making of the queryparameters easier. 

In the Post you pass the data in the send method of the XMLHttpRequest in the GET method you add the data as queryparameters to the url.

 [1]: http://www.w3schools.com/tags/ref_httpmethods.asp "W3Schools"