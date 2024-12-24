---
title: Handling Unauthenticated AJAX Requests
author: Alex Ullrich
type: post
date: 2012-02-15T11:12:00+00:00
ID: 1521
excerpt: 'A common pattern that I use in creating ajaxy applications is to return a small HTML fragment from the request, and then inject this fragment into the DOM in the callback executed after a successful request.  This tends to be a bit simpler than returnin&hellip;'
url: /index.php/webdev/uidevelopment/handling-unauthenticated-ajax-requests/
views:
  - 7964
rp4wp_auto_linked:
  - 1
categories:
  - AJAX
  - ASP.NET
  - UI Development
tags:
  - ajax
  - asp.net mvc
  - jquery

---
A common pattern that I use in creating ajaxy applications is to return a small HTML fragment from the request, and then inject this fragment into the DOM in the callback executed after a successful request. This tends to be a bit simpler than returning JSON and picking it apart to update the page, but it has one major problem, at least when using normal forms authentication. If the user gets logged out (either by logging out from another tab or an expiring session), the AJAX request gets redirected to the login page, which is then returned and inserted into the page. You can see how hideous this can become in the picture below.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/WebDev/handling-unauthenticated-ajax-requests/bad-logon.PNG?mtime=1328983889"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/WebDev/handling-unauthenticated-ajax-requests/bad-logon.PNG?mtime=1328983889" width="941" height="644" /></a>
</div>

In this case, when you sign up for a task it is supposed to return the updated task, and use this task to replace the notecard into the DOM on the right hand side. It can actually get uglier, as we support signing up for multiple tasks at a time. However, if the session expires we get a big, ugly login page displayed in the midst of all our pretty notecards. What I'd like to find is a way to retain the convenience of using forms authentication, but handle scenarios like this more gracefully.

Adding a piece of metadata to the login page seemed like a good way to get this done without making things any harder on the user. I initially wanted to get the login page classified as an error, so that redirection could be accomplished on the client side using the error callback available when using jQuery for AJAX requests. This would be nice, but in jQuery 1.5 and above a "statusCode" callback has been added that is even nicer. You can use the callback like this:

```javascript
$.ajax({
  statusCode: {
    404: function() {
      alert('page not found');
    }
  }
});
```

The ease with which this allows you to define behavior for different status codes is fantastic. As I set off down this path, the most obvious choice seemed to be adding a 401 (unauthorized) status code to the login page, but this got us into a weird redirect loop because forms authentication redirects all 401's to the login page – causing you lose the return URL, and redirect users back to the login page once they are authenticated. Not exactly a paragon of usability.

Having found this out the hard way, I decided a custom status code might be better. It's easy enough to add the custom status code to the login page with a single line of C#:

```csharp
Response.StatusCode = 999;
```

We can then make our requests using something along these lines:

```javascript
$.ajax({
    type: 'POST',
    url: '/Task/SignUp',
    data: 'projectId=' + pid + '&storyId=' + sid + '&id=' + id + '&initials=' + initials,
    success: function (html) {
        $('#' + id).replaceWith(html);
    },
    statusCode: {
        999: function() {
            location.href = '/Account/LogOn?returnUrl=' + location.href;
        }
    }
});
```

This works well enough, at least from cassini. When deployed to an IIS server however, we noticed that the 999 status code was getting picked up by the error handling modules, and of course we did not have an error page set up for that code. To get around that we had to add the following to the code to display our login view:

```csharp
Response.TrySkipIisCustomErrors = true;
```

That's kind of nasty, but it seems to allow us to accomplish our goal. I think I can stomach it on this one page in the name of improving the user's experience on the site. 

There's one more thing we can do to make our lives easier. There is no special behavior in our statusCode handler, so it would be nice define it only once. Luckily, the folks at jQuery are a step ahead of us. We can define our statusCode handler using the ajaxSetup method in our master page:

```javascript
$.ajaxSetup({
    statusCode: {
        999: function () {
            location.href = '/Account/LogOn?returnUrl=' + location.href;
        }
    }
});
```

Now that everything is set up, we are properly redirected to the login page:

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/WebDev/handling-unauthenticated-ajax-requests/good-logon.PNG?mtime=1328987447"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/WebDev/handling-unauthenticated-ajax-requests/good-logon.PNG?mtime=1328987447" width="557" height="435" /></a>
</div>

I'm not sure this is the best solution to our problem, but it is certainly a solution. It allows us to keep leveraging ASP.net's built in error handling and authentication (I know they aren't perfect, but they are good enough for us in this scenario) while making the user's life a bit easier in the event something goes wrong. In this case, my team is the primary user of the application so it makes our lives easier 🙂

Complete source code for the application in question is available on [github][1].

 [1]: https://github.com/jawsthegame/PivotalExtension