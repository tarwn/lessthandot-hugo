---
title: 'Nancy and C# uploading and showing an image'
author: Christiaan Baes (chrissie1)
type: post
date: 2012-12-28T11:36:00+00:00
ID: 1887
excerpt: Letting the user upload an image and showing that image on the screen.
url: /index.php/webdev/serverprogramming/nancy-and-c-uploading-and/
views:
  - 15381
categories:
  - ASP.NET
  - Server Programming

---
## Introduction

I have been using Nancy for a few weeks now and made a demo application that you can [find on Github][1].

Today I will add the ability for you to add a picture to a user.

## The upload

For this we need to change our UsersModule and add a few methods.

First we add a get so that we can show a page with the userdata and an upload form. Something like this.

```csharp
Get["/users/userimageupload/{Id}"] = parameters =&gt;
            {
                Guid result;
                var isGuid = Guid.TryParse(parameters.id, out result);
                var user = userService.GetById(result);
                if (isGuid && user != null)
                {
                    return View["UploadUserImage",user];
                }
                return HttpStatusCode.NotFound;
            };
            ```
Nothing special there, we just ask for the Id, check if it is there and if it is we show the view.
  
This will use the following view.

```xml
@inherits Nancy.ViewEngines.Razor.NancyRazorViewBase&lt;NancyDemo.Csharp.Model.UserModel&gt;
@{
    ViewBag.Title = "Add user image page";
    Layout = "Master.cshtml";
}


@section menu
{
Rendersection("menu")
}
@section menuitems
{
    &lt;a href="/users"&gt;Users&lt;/a&gt;
}

&lt;h1&gt;Welcome to the add user image page&lt;/h1&gt;
&lt;table&gt;
    &lt;tr&gt;
        &lt;td&gt;Id&lt;/td&gt;
        &lt;td&gt;@Model.Id&lt;/td&gt;
    &lt;/tr&gt;
    &lt;tr&gt;
        &lt;td&gt;Name&lt;/td&gt;
        &lt;td&gt;@Model.Name&lt;/td&gt;
    &lt;/tr&gt;
    &lt;tr&gt;
        &lt;td&gt;RealName&lt;/td&gt;
        &lt;td&gt;@Model.RealName&lt;/td&gt;
    &lt;/tr&gt;
    &lt;tr&gt;
        &lt;td&gt;Password&lt;/td&gt;
        &lt;td&gt;@Model.Password&lt;/td&gt;
    &lt;/tr&gt;
&lt;/table&gt;
&lt;form action="/users/userimageupload/@Model.Id" method="post" enctype="multipart/form-data"&gt;
    &lt;input name="upload" type="file" size="40" /&gt;
    &lt;input type="submit" value="Post!" /&gt;
&lt;/form&gt;
```
This will look something like this.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/nancy/nancy16.png?mtime=1356700759"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/nancy/nancy16.png?mtime=1356700759" width="518" height="247" /></a>
</div>

All the userdata is shown and the form to upload the file.

Now we need a Post method in our module to intercept when the user clicks on post.

```csharp
Post["/users/userimageupload/{Id}"] = parameters =&gt;
                {
                    Guid result;
                    var isGuid = Guid.TryParse(parameters.Id, out result);
                    var user = userService.GetById(result);
               
                    var file = this.Request.Files.FirstOrDefault();

                    if (isGuid && user != null && file != null)
                    {
                        var fileDetails = string.Format("{3} - {0} ({1}) {2}bytes", file.Name, file.ContentType, file.Value.Length, file.Key);
                        user.FileDetails = fileDetails;
                        var filename = Path.Combine(pathProvider.GetRootPath(), "Images", user.Id + ".jpeg");

                        using (var fileStream = new FileStream(filename, FileMode.Create))
                        {
                            file.Value.CopyTo(fileStream);
                        }
                        return View[user];
                    }
                    return HttpStatusCode.NotFound;
                };```
This accepts an Id as parameter. and saves the first file in the stream to a folder in our project called Images and saves the filedetails in the user object. You can also see that we use the pathprovider. We can inject this pathprovider in our module via the constructor like this.

<code class="codespan">public UsersModule(UserService userService, IRootPathProvider pathProvider)</code>

And as you can see we return to our userpage.

## The user

The userpage needs to show the image.

We do this by adding a Get to our usermodule.

```csharp
Get["/users/getimage/{Id}"] = parameters =&gt;
                {
                    Guid result;
                    var isGuid = Guid.TryParse(parameters.Id, out result);
                    var filename = Path.Combine(pathProvider.GetRootPath(), "Images", result.ToString() + ".jpeg");
                    if (!File.Exists(filename)) filename = Path.Combine(pathProvider.GetRootPath(), "Images", "emptyuser.jpeg");
                    var stream = new FileStream(filename, FileMode.Open);
                    return Response.FromStream(stream, "image/jpeg");
                };```
This looks to see if an image exists and if not than it shows a default image.

With picture.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/nancy/nancy17.png?mtime=1356701376"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/nancy/nancy17.png?mtime=1356701376" width="654" height="662" /></a>
</div>

With default picture.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/nancy/nancy18.png?mtime=1356701492"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/nancy/nancy18.png?mtime=1356701492" width="365" height="425" /></a>
</div>

This means our view is changed a little bit.

```xml
@inherits Nancy.ViewEngines.Razor.NancyRazorViewBase&lt;NancyDemo.Csharp.Model.UserModel&gt;

@{
    ViewBag.Title = "User page";
    Layout = "Master.cshtml";
}

@section menu
{
    RenderSection("menu")
}
@section menuitems
{
    &lt;a href="/users"&gt;Users&lt;/a&gt;
}

    &lt;h1&gt;Welcome to the user page&lt;/h1&gt;
&lt;table&gt;
    &lt;tr&gt;
        &lt;td&gt;Id&lt;/td&gt;
        &lt;td&gt;@Model.Id&lt;/td&gt;
    &lt;/tr&gt;
    &lt;tr&gt;
        &lt;td&gt;Name&lt;/td&gt;
        &lt;td&gt;@Model.Name&lt;/td&gt;
    &lt;/tr&gt;
    &lt;tr&gt;
        &lt;td&gt;RealName&lt;/td&gt;
        &lt;td&gt;@Model.RealName&lt;/td&gt;
    &lt;/tr&gt;
    &lt;tr&gt;
        &lt;td&gt;Password&lt;/td&gt;
        &lt;td&gt;@Model.Password&lt;/td&gt;
    &lt;/tr&gt;
    &lt;tr&gt;
        &lt;td&gt;FileDetails&lt;/td&gt;
        &lt;td&gt;@Model.FileDetails&lt;/td&gt;
    &lt;/tr&gt;
&lt;/table&gt;
&lt;a href="/users/userimageupload/@Model.Id"&gt;Upload image&lt;/a&gt;&lt;br /&gt;
&lt;img src="/users/getimage/@Model.Id"/&gt;
```
And that&#8217;s it

## Conclusion

It&#8217;s not all that hard to do, once you know how.

Special thanks to [jchannon][2], [Grumpydev][3] and [TheCodeJunkie][4] for the help.

 [1]: https://github.com/chrissie1/NancyVB
 [2]: https://twitter.com/jchannon
 [3]: https://twitter.com/Grumpydev
 [4]: https://twitter.com/TheCodeJunkie