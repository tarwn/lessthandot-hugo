---
title: WebMatrix – Routing and Magic Pages
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2011-07-07T09:42:00+00:00
ID: 1243
excerpt: "So I've been working on a practice site using WebMatrix. The basic premise was that I would create a site that had some similar functionality to Delicious to help me track the various articles, podcasts, books, and so on. This would also give me something practical to work on as I try to get a handle on this whole WebMatrix thing."
url: /index.php/webdev/serverprogramming/aspnet/webmatrix-routing-and-magic-pages/
views:
  - 17485
rp4wp_auto_linked:
  - 1
categories:
  - ASP.NET
tags:
  - razor
  - webmatrix

---
So I've been working on a practice site using WebMatrix. The basic premise was that I would create a site that had some similar functionality to Delicious to help me track the various articles, podcasts, books, and so on. This would also give me something practical to work on as I try to get a handle on this whole WebMatrix thing.

So here's a couple tips, tricks, and the occasional slip in the mud I picked up along the way.

Chrissie started us off with [this post][1], so I'm skipping right past the making of files and digging into the first layer of how things work (and/or how I broke them).

_Note: I know in my [last post][2] I said the application was WebMatrix and the programming framework was WebPages, but no one calls it WebPages (apparently that name is hard to search for or something), so for the remainder of this post I'll call it WebMatrix. Feel free to call me names later._

## Magic Routing

The first problem I had when I deployed my site to a live host was getting the darn pages to work. No matter how I typed the paths, the cshtml pages wouldn't stop reporting a 404. So of course I started digging into the IIS settings. And fixed the meta settings. And fixed the extension. And fixed something else. And then found out I wasn't supposed to fix it and the real problem was the guy sitting behind my keyboard typing instead of reading the manual. Oops.

When we build a razor site (or is it a WebPages site? WebMatrix site? argh…), it takes advantage of additions in the 4.0 Framework to provide routing automatically. Every cshtml file we create automatically becomes part of the route check when a request comes into IIS. This is similar to the ASP.Net MVC routing, but instead requires no up front work, running 100% on pixie dust.

Say I ask for http://MyAwesomeSite.com/blueberries/and/cream. Note that we don't include the .cshtml extension. Behind the scenes, my webserver basically has this conversation with itself:

> Do I have a file named /blueberries/and/cream.cshtml?
  
> No.
  
> How about /blueberries/and.cshtml?
  
> Nope.
  
> How about /blueberries.cshtml?
  
> Nope.
  
> Maybe there was a default file at the root level?
  
> Ah, ok.
  
> Are you busy tomorrow night…
  
> Yep.
  
> Oh…er, ok, well here's your file then. 

The most specific file match wins, without the need for setting up routing ahead of time. Simply drop an appropriately named file in a folder and point an href at it. 

Any trailing portion of the URL is then stored in an array for easy access (which we will see later). What this allows us to do is create very easy, semantic URLs. 

Example: my application will show me the list of all activities when I go to http://notmyrealurl.com/Activities but will filter the list for the “razor” tag when I go to http://notmyrealurl.com/Activities/razor.

The exception is files that begin with underscores. Underscored files are not accessible, as they are used to protect our Magic Files in the next section.

## Magic Pages

Ok, so this part I'm less enamored of, but there are some good parts. There are two classes of special files in WebMatrix, layout files and lifecycle pages. 

### Layout Files

Layout files allow us to define a common layout that we want to apply to our website, basically a template or master file. Inside the layout file we can define where we want the main body to be rendered (the file that was requested), as well as additional required or optional sections the original page needs to provide. A minimal layout file would look something like this:

```cshtml
<!DOCTYPE html>
<html>
  <head>
	<title> @PageData["Title"] </title>
  </head>
  <body>
	@RenderPage("~/Shared/_Header.cshtml")
	<div id="sidepane">
		@RenderSection("SidePane", required: false)
	</div>
	<div id="main">
	  @RenderBody()
	</div>
	</div>
	@RenderPage("~/Shared/_Footer.cshtml")
</body>
</html>
```
A basic page that includes a header file, an optional section named “SidePane”, and body of the original page, and finally an included footer file. In this sample I am keeping my \_Header, \_Footer, and _MainLayout files in a subfolder called Shared. To use this layout, I could make a sample page like this:

```cshtml
@{
Layout = "~/Shared/_MainLayout.cshtml";
PageData["Title"] = "My Awesome Hello World";
}
<b><blink>This stuff is in my main body, Hello!</blink></b>
```
And if I wanted to both show off the presence of any extra URL data you put in the URL as well as the optional sidepane?

```cshtml
@{
Layout = "~/Shared/_MainLayout.cshtml";
PageData["Title"] = "My Awesome Hello World";
}
<b><blink>This stuff is in my main body, Hello!</blink></b>

@section SidePane{
	@if(UrlData.Count > 0){
        <text>
		<marquee>Bam!</marquee>
        First one: @UrlData[0]<br/>
        All of them: @(String.Join(",",UrlData))<br/>
        </text>
	}
}
```
Yeah, I just used a blink and a marquee, that's how exciting this part is. You'll notice I also put a value in the PageData dictionary at the top of the page, which was then regurgitated by the layout and used as the title. When files are being rendered or included, they receive a copy of the PageData and can access the values we squirreled away. Handy.

There's also a Page dynamic property in WebBasePage that we can assign stuff to. So far that hasn't blown up in my face.

If I think back to the classic ASP days I can remember a time when we desperately wanted this type of functionality. A clean, straightforward way to define some sitewide layout templates separately from our actual page, and here it is. It's like ASP 4.0.

And I only just now noticed that interesting conjunction in ASP and ASP.Net numbering schemes, hmm.

### Lifecycle Pages

Then there are the lifecycle pages. Where layout files give us a handy way to do templating, these provide us some basic global capabilities for the site. There are three main files that we are concerned with:

~/web.config
:   This holds configurations for the site, like any ASP.Net site

~/_AppStart.cshtml
:   Contains logic to run the first time any page is requested from the site

~/_PageStart.cshtml
:   Contains logic to run when a page in the current or child directory is requested

AppStart is handy for setting values or creating objects that will have global application scope. For example, when using the SimpleSecurity helpers, you will want to put an initialization call here to initialize the authentication system. 

_And if you are me you will comment them out when you find out your webhosts machine.config fubars settings relevant to ASP.Net membership._

PageStart is trickier in a couple ways. First, PageStart is a bit of a misnomer. It does execute at the start of the page, but it can also be convinced to encapsulate the execution of the requested page, running both before and after the main page is rendered. 

```cshtml
@{
    <b>I'm Before</b>
    RunPage();
    <text>I'm after</text>
}
```
If you specifically include the RunPage() line, then your page will be run in between the code above and below it. If you don't include this directive then the entire PageStart file will be run before calling the requested page.

The next trick is that \_PageStart runs in a nested sequence from the root level to the level of the file that has been requested. Basically the engine traverses each directory from the root to the folder your requested file is in and, if a \_PageStart file is present, executes it. It's similar to those nesting dolls (or recursion, oohh).

But the magic is not done yet, and this was one of those pits of mud I fell into.

Even though you can wrap the execution of the page inside a _PageStart, the execution of the page and PageStart finish before the Layout is applied and executed.

Lets try that again (I lost 2 hours of my life to this feature).

Say we have a \_PageStart that wraps around the RunPage call, a Main page, and a referenced \_LayoutMain file and we request http://myevilpitofmud.com/Main. The execution would look like:

  1. Top part of _PageStart
  2. Main file
  3. Bottom part of _PageStart
  4. Layout file

Yeah.

## But what else did you do?

There's more. I've spent 17 hours on my sample site so far, which includes implementing a SQL Compact database, implementing and removing WebSecurity authentication, and a score of other things. I've found out the hard way that getting fancy can bite you and that it is actually possible to install a 10 inch tailpipe on the WebMatrix hatchback (which I just realized could refer to two different things I've done, hmmm, tailpipe and a <strike>shopping cart handle</strike> spoiler then). 

Unfortunately because I had issues with the authentication I can't post a live link yet, the next post will have to cover all the fun that has gone into trying to get a certified host for WebMatrix to run correctly (hint, not all [&#8216;SpotLight' hosts][3] can run the included, basic example sites).

 [1]: /index.php/WebDev/UIDevelopment/AJAX/trying-out-webmatrix-and-razor "Chissie on Trying Out Webmatrix and Razor"
 [2]: /index.php/WebDev/ServerProgramming/the-many-functions-of-webmatrix "Read the Webmatrix Overview post"
 [3]: http://www.microsoft.com/web/hosting/home "See hosting options at Microsoft"