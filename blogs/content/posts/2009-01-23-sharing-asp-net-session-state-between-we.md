---
title: Sharing ASP.net Session State Between Web Applications With SQL Server – Part I
author: Alex Ullrich
type: post
date: 2009-01-23T17:15:00+00:00
excerpt: "I recently needed to implement a shared session between a few different applications, and didn't find a whole lot on the interwebs about how to do this (I'm starting a second life as a web developer though, it could be that I just don't know all the ter&hellip;"
url: /index.php/webdev/serverprogramming/aspnet/sharing-asp-net-session-state-between-we/
views:
  - 32663
rp4wp_auto_linked:
  - 1
categories:
  - ASP.NET
tags:
  - asp.net
  - mvc
  - sqlserver
  - webservices

---
I recently needed to implement a shared session between a few different applications, and didn&#8217;t find a whole lot on the interwebs about how to do this (I&#8217;m starting a second life as a web developer though, it could be that I just don&#8217;t know all the terms the cool kids are using). It ended up being a bit easier than I thought it would be, but then again everything is easy when the great folks at LessThanDot&#8217;s [ASP.net Forum][1] are there to bail me out 😉

Anyway, because I had trouble finding this information I figured it was worth throwing together a blog post to get it all in one place. I&#8217;ll try to cover everything here, including the session state installation. In order to test, I set up an ASP.net MVC project (using the **beta** release), an ASP.net webservice project, and a local SQL Server instance to maintain the session state (all this is running on the one computer for now, so it feels silly, but remember the goal is to eventually get each component onto its own server. I named them &#8220;SessionTest.Services&#8221; and &#8220;SessionTest.Site&#8221;. So go ahead and set that up, I&#8217;ll still be here I swear.

Wow, you are quick! But you&#8217;re just getting started, there&#8217;s some more things to do. First, lets install the SQL Server session state on localhost. You&#8217;ll need to fire up command prompt to do this and run the command: 

<code class="codespan">C:WindowsMicrosoft.NETFrameworkv2.0.50727aspnet_regsql.exe -ssadd -sstype c -S ALEXCOMPUTER -d ASPState -E</code>

The parameters here mean:

  * **ssadd:** Add Session State
  * **sstype c: Session State type = &#8220;custom&#8221; (not really, but we need to rename the database)**
  * **-S ALEXCOMPUTER:** Server to install the session state db on. I already have it installed on mine, so you can go ahead and replace this with your computer name. I really appreciate the offer though
  * **d ASPState:** Name for the database (this is what ASP.net expects)
  * **E:** Signal for the Session State database to use current windows credentials for authentication (when you deploy to the server, you&#8217;ll need to grant access to the service account that ASP.net is running under

It&#8217;ll take a minute or so, but eventually it will finish, and it will tell you so. When its&#8217; done, lets mosey on over to Visual Studio.

First thing to do here is add a reference to your class library project to both the webservice and the the website project, so that we can share types. Or type in this case. May as well add that type to the class library now as well.

First thing we want to do is get the MVC project ready. I got rid of everything in the Views folder except for the About and Index pages in Views/Home. I also got rid of the Account controller. Feel free to do this if you want, but I suppose it isn&#8217;t necessary. One thing that is necessary, since we want to use jQuery to get the webservice value eventually, is to reference the jQuery script. So just add this to the bottom of the page called Site.Master (before the closing html tag):

<code class="codespan">&lt;script type="text/javascript" src="../../Scripts/jquery-1.2.6.min.js" /&gt;</code>

Next, we want to add a simple form to Index.aspx. This form just has one text input, and a submit button.

<pre>&lt;form id="mainForm" runat="server" action="/Home/Entered"&gt;
        &lt;p&gt;Give it a try: &lt;input type="text" name="inputValue" /&gt;&lt;/p&gt;
        &lt;input type="submit" id="submitter" /&gt;
&lt;/form&gt;</pre>

You notice its&#8217; action is the page Home/Entered, which currently does not exist. So we need to add a new MVC Content Page with that name. In the page attributes, set EnableViewStateMac=&#8221;false&#8221;. Within the content place holder, we can add this HTML:

<pre>&lt;p&gt;Entered Value was: &lt;%= Session["enteredValue"].ToString() %&gt;&lt;/p&gt;
&lt;p&gt;Try getting it from the service:&lt;/p&gt;
&lt;p&gt;&lt;input type="button" id="retrieveButton" text="retrieve it!" onclick="retrieve()" /&gt;&lt;input type="text" id="retrievedValue" /&gt;&lt;/p&gt;
&lt;script type="text/javascript"&gt;
    function retrieve() {
    }
&lt;/script&gt;</pre>

The javascript is not implemented yet to retrieve the value yet, but at this point there is just one thing we need to do to get the pages working, and that is add a new ActionResult to the HomeController for our new &#8220;Entered&#8221; page. All this action will really be doing is placing the input from the form submission into the session. So, 

<pre>public ActionResult Entered(String inputValue)
{
    Session["enteredValue"] = inputValue;
    return View();
}</pre>

Now, you can run the page and you should be able to enter a value in Index, and see it on the &#8220;Entered&#8221; page. So, we can tell that our traditional (in process) session is working. Now let&#8217;s change it to use the database. In the main web.config (not the one in the Views folder) we&#8217;ll need to add a SessionState entry within system.web (I usually do this at the bottom, having a common place to look makes my life easier). So we add this entry:

<code class="codespan">&lt;sessionState mode="SQLServer" sqlConnectionString="Data Source=127.0.0.1; Integrated Security=SSPI" cookieless="false" timeout="20"/&gt;</code>

Now, as long as your local SQL Server instance is accepting TCP/IP connections, it should work just as before. And this post is getting long, so this will be the end of part I.

Stay tuned for the exciting conclusion, where we will set up the webservice and the client-side interaction. In fact, tune in here:[Sharing ASP.net session state with SQL Server, Part II][2]

Got a question on ASP.net? Check out our [ASP.net Forum][1]!

 [1]: http://forum.ltd.local/viewforum.php?f=27
 [2]: /index.php/WebDev/ServerProgramming/ASPNET/sharing-asp-net-session-state-between-ap