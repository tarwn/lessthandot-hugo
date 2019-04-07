---
title: The Many Functions of WebMatrix
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2011-06-23T11:42:00+00:00
ID: 1226
excerpt: "Several weeks ago I started digging into WebMatrix. Over the course of a weekend I was able to put together a quick, functioning website, pick up some basics of working with WebMatrix and the deployment tool, and play with a few other technologies as well. Then I stepped back and realized I was only using a percentage of it's capabilities."
url: /index.php/webdev/serverprogramming/the-many-functions-of-webmatrix/
views:
  - 23655
rp4wp_auto_linked:
  - 1
categories:
  - ASP.NET
  - Classic ASP
  - Server Programming
tags:
  - asp.net
  - razor
  - webmatrix

---
Several weeks ago I started digging into WebMatrix. Over the course of a weekend I was able to put together a quick, functioning website, pick up some basics of working with WebMatrix and the deployment tool, and play with a few other technologies as well. Then I stepped back and realized I was only using a percentage of it&#8217;s capabilities.

I&#8217;ve read a lot of blogs and articles on Web Matrix and none of them prepared me for the sheer range of capabilities this little tool offers. One would mention writing a web page, another would talk about deployment, a third about WordPress&#8230;it wasn&#8217;t until I started playing with it and went to write a blog entry of my own that I realized how much capability was packed into this one &#8216;little&#8217; tool. 

This isn&#8217;t a deep dive, there&#8217;s plenty of those. This is a shallow float across the surface of what is actually a quite impressive (and don&#8217;t forget free) tool.

## What is WebMatrix?

WebMatrix is a free web development tool from Microsoft that helps you create and publish websites.

So is it a tool? A set of templates? A language? A publication engine?

Yes.

And an IIS express manager, database configurator, generator of CMS-driven sites &#8230;The kitchen sink is in there somewhere.

## Building Sites

WebMatrix works around the concept of a Site. A site can start as an entry in the gallery of pre-built packages or as a template and/or blank code files. 

<div style="border: 1px solid #AAAAAA; font-size: 80%; color: #666666; text-align: center; padding: 8px; margin: 8px;">
  <img src="http://tiernok.com/LTDBlog/webmatrix/WebGallery.png" alt="Web Gallery" style="padding-bottom: 5px;" /><br /> Web Gallery in Web Matrix
</div>

The Gallery option presents a list of 52 CMS, blogging, eCommerce, (the list goes on) packages that can be installed simply by selectig them. If I select the WordPress option, WebMatrix detects that I don&#8217;t have MyQSL installed and asks if I want to install it or have access to a remote installation. Other gallery options offer similar interactions for their own requirements, with the goal being a completely running system in only a few clicks. In the case of WordPress it will also detect whether you have PHP installed (more on that later) and install that as well.

<div style="border: 1px solid #AAAAAA; font-size: 80%; color: #666666; text-align: center; padding: 8px; margin: 8px;">
  <img src="http://tiernok.com/LTDBlog/webmatrix/Installing.png" alt="WordPress Install" style="padding-bottom: 5px;" /><br /> Installing WordPress
</div>

With a download, a few button clicks, administrative permissions, and entry of some basic settings, I just installed a fully customizeable version of WordPress. Which is really cool, but also kind of scary if you think about how little technical knowledge I really needed and that there are dozens of other packages available.

_Note: it was at this point I noticed my MySQL installation was annoyed and not running properly, leaving me stuck partway on the WordPress Install. So a big YMMV may be necessary here, as it seems my skill at causing things to break has managed to break yet another &#8220;Next, Next, Next&#8221; wizardy dialog._

**More Depth:** [A WordPress Blog in 15 Minutes with WebMatrix][1]

## Building Sites &#8211; But Wait, There&#8217;s More

Ok, so what if we want to build our own site?

<div style="border: 1px solid #AAAAAA; font-size: 80%; color: #666666; text-align: center; padding: 8px; margin: 8px;">
  <img src="http://tiernok.com/LTDBlog/webmatrix/SiteFromTemplate.png" alt="Creating Site From Template" style="padding-bottom: 5px;" /><br /> Creating Site from Template
</div>

The other side of the Site creation process is the option to create one from a template. This offers five options ranging from &#8220;Empty Site&#8221; to &#8220;Start Site and even &#8220;Bakery&#8221;. If you start with one of the non-Empty sites you will be given all the files and folders for a fully functioning site. These sites are based on the new WebPages framework and written in the C#-style razor syntax. The WebPages framework was released in conjunction with WebMatrix and joins Web Forms and MVC as an available ASP.Net framework.

If we select the empty site option we&#8217;ll be presented with one lonely little robots.txt file and an empty folder. The interesting part is what happens when we ask to add a file. We receive a file creation dialog (which should be familiar to Visual Studio users) and, given what I have seen so far and my Visual Studio background, I expect a list of CSHTML files, CSS, and maybe a JS or HTML. 

Nope.

HTML, CSS, JScript, and CSHTML are present, but so are (huge breath): VBHTML, 2 ASPX&#8217;s (VB + C#), Classic ASP, PHP, TXT, XML, 2 Class Files (VB + C#), 3 Global ASA[X]&#8217;s, 3 Master Pages, SQL, User Controls (VB + C#), and 4 web configs (.Net 2, 3, 3.5, 4).

The CSHTML and VBHTML options are using the new [razor engine][2] and this is the most commonly documented option both on the [ASP.Net website][3] and in blogs. The PHP option offers you a choice between installation of PHP 5.2 and 5.3 (enable it in IIS by checking the lonely PHP checkbox in the IIS/site settings) and away we go with some PHP-ing. 

And to be honest I haven&#8217;t even had time to try the rest, but they all seem fairly self-explanatory and, if I could get PHP running in a new MS web tool in just a few moments, I don&#8217;t expect to see too many surprises from the cast of MS Web technologies. Although I do want to try the Classic ASP one, for nostalgia if nothing else.

**More Depth:** [Web Matrix Tutorials (CSHTML) at ASP.Net][4]
  
**More Depth:** [Creating PHP Websites with WebMatrix][5]
  
**More Depth:** [Trying Out WebMatrix and Razor][6] by Chrissie
  
**More Depth:** Not finding a good Classic ASP link&#8230;

Unfortunately intellisense is either non-existent or just extremely limited and not noticeable after a day using Visual Studio. However, if you have Visual Studio available then you can click the Visual Studio button on the WebMatrix tool bar and the project and file you are currently working on will immediately open in Visual Studio, allowing you to take advantage of it&#8217;s more extensive editing (and of course intellisense) capabilities.

## Running and Debugging Sites

With so many different options, running these sites must require jumping through a hoop, on fire, balancing a water balloon on our nose, right?

Not so much.

WebMatrix uses IIS Express internally and offers a simple interface to hook into just a few settings that will cover most of our needs. Press the &#8220;Run&#8221; button and we&#8217;re greeted with the current page run in whatever our default browser is. 

<div style="border: 1px solid #AAAAAA; font-size: 80%; color: #666666; text-align: center; padding: 8px; margin: 8px;">
  <img src="http://tiernok.com/LTDBlog/webmatrix/Toolbar.png" alt="WebMatrix Files Toolbar" style="padding-bottom: 5px; max-width: 750px;" /><br /> WebMatrix Files Toolbar
</div>

PHP? CSHTML? Text file? Doesn&#8217;t matter. Because IIS Express is running in the background, all WebMatrix has to do is fire a URL at a browser and we&#8217;re there. 

Another advantage to this setup is that we don&#8217;t have to dig through yet another revision of the IIS management interface. Which is great for me, as I have been downgraded to just a programmer for the last few years and left most of my IIS management experience rusting away on the older IIS 6/5/4-style interface.

<div style="border: 1px solid #AAAAAA; font-size: 80%; color: #666666; text-align: center; padding: 8px; margin: 8px;">
  <img src="http://tiernok.com/LTDBlog/webmatrix/requests.png" alt="WebMatrix Requests Page" style="padding-bottom: 5px;" /><br /> IIS Requests Page
</div>

Basic settings are available to manage our default site pages, enable PHP, change the .Net framework version, enable SSL, and so on. A page is also available to view the HTTP requests that are being made to the IIS instance, letting us see the lonely calls for a favicon go forever unanswered or the execution time for each individual HTTP request.

## Managing Data

So we have web pages and a way to host them, what about managing the database behind the site? Yep, WebMatrix is there too.

The Databases tab will show you any connections we have configured for the site, as well as any SQL Compact databases (SDF files) available to the site. In our WordPress example this means we have a configured connection to a MySQL (5?) database. For my first sample site I have a SQL Compact database with the ability to manage the tables from inside Web Matrix. I haven&#8217;t connected to SQL Server from inside yet, so I can&#8217;t say whether it offers just a view of the connection, like MySQL, or more extensive management of the tables and queries like SQL Compact or an MDF in Visual Studio.

<div style="border: 1px solid #AAAAAA; font-size: 80%; color: #666666; text-align: center; padding: 8px; margin: 8px;">
  <img src="http://tiernok.com/LTDBlog/webmatrix/database.png" alt="SQL Compact View" style="padding-bottom: 5px;" /><br /> SQL Compact DB
</div>

Later on when we deploy we get an interesting option for SDF files. They are treated separately from the main files and are not automatically selected as part of the sync (limiting the chances of a low-coffee-foot-shooting incident).

**More Depth:** <a href=http://www.microsoft.com/web/post/connecting-to-a-sql-server-or-mysql-database-in-webmatrix"" title="Connecting to a SQL Server or MySQL Database in WebMatrix">Database Connections at Microsot.com</a>
  
**More Depth:** [Everything SQL Server Compact site][7]
  
**More Depth:** [Code-First Development (EF) w/ SQL CE 4.0][8] &#8211; I wonder, if you do it DB-first is the code actually larger than the DB?

## Site Reports

To be honest I haven&#8217;t spent more than about 15 minutes in the site reports because there is just so much to Web Matrix.

I had difficulty creating reports for some sites because it seemed like the report engine would immediately run out and start trying to index the internet. Once I got that under control I was able to run a report of my site and get back some meaningful (and some not) information.

<div style="border: 1px solid #AAAAAA; font-size: 80%; color: #666666; text-align: center; padding: 8px; margin: 8px;">
  <img src="http://tiernok.com/LTDBlog/webmatrix/reports.png" alt="Reports" style="padding-bottom: 5px;" /><br /> WebMatrix Reports
</div>

Basically the reporting mechanism attempts to walk through the entire site, gathering statistics on the amount of time each page took to run as well as SEO warnings and errors on each page. The output of the the SEO and Performance data comes with a slider that lets you dynamically filter the output from all information to just the most important subset.

<div style="border: 1px solid #AAAAAA; font-size: 80%; color: #666666; text-align: center; padding: 8px; margin: 8px;">
  <img src="http://tiernok.com/LTDBlog/webmatrix/slider.png" alt="Silder" style="padding-bottom: 5px;" /><br /> Report Slider
</div>

For the SEO errors, selecting an individual error displays more information about the error. Probably the most consistent warning I received was leaving the the meta description tag off every page I had written (guilty as charged). Examples of other errors included broken hyperlinks, mixed canonical formats, and just plain bad URLs. 

**More Depth:** [Webmatrix Reports Workspace Help][9]
  
**More Depth:** [Use WebMatrix to optimize your site for search engines][10]

## Deploying a Site

Web Matrix uses [WebDeploy][11], doing away with all the manual file dragging or xcopying. On the initial deployment we are asked to enter settings in for our host or, if we don&#8217;t have a host, the tool points us to a [host shopping page][12] to help us find one. Once we have a host, we return to the typical WebMatrix approach of only needing a few settings (instead of a reference manual the size of my car).

<div style="border: 1px solid #AAAAAA; font-size: 80%; color: #666666; text-align: center; padding: 8px; margin: 8px;">
  <img src="http://tiernok.com/LTDBlog/webmatrix/publish1.png" alt="Publishing" style="padding-bottom: 5px;" /><br /> WebMatrix Publishing
</div>

The initial deployment only takes a few settings or, if you are like me and have a host that supports it, the download of a settings file. The only major hiccup I ran into was that at one point I had a SQL Compact database open in Visual Studio (or maybe just locked, not sure) and this caused all kinds of deployment problems. I have since decided that closing visual studio while deploying seems to be the safe, pain-free route.

Deployments after the initial one are not full site deployments. The tool will scan for changes that haven&#8217;t been deployed yet and provide us with the option of selecting which changes we would like to deploy. It automatically selects all file changes, but leaves SQL Compact files unchecked. 

<div style="border: 1px solid #AAAAAA; font-size: 80%; color: #666666; text-align: center; padding: 8px; margin: 8px;">
  <img src="http://tiernok.com/LTDBlog/webmatrix/publish2.png" alt="Publishing" style="padding-bottom: 5px;" /><br /> WebMatrix Publishing
</div>

One problem I have run into, coming from a larger scale database background, was that sometimes I would forget to download a fresh copy of the database before making schema changes (so I then had to download the database, make the changes again, then deploy it back to the live site).

**More Depth:** [Publish Your Website &#8211; IIS.Net][13]
  
**More Depth:** [Microsoft WebMatrix in Context and Deploying Your First Site &#8211; Scot Hanselman][14]

## The Bad News

Not all is perfect in WebMatrix land. There are a lot of developers that will dismiss it (and anyone using it) out of hand because they see it as a step backwards (it&#8217;s ok, though, you probably don&#8217;t want to see the code they&#8217;ve been writing anyway). 

Perhaps the biggest problem I ran into while using it was the fact that the interface occasionally went wonky on my quad-core, 10GB of RAM system w/ a fairly expensive graphics card. Apparently this is due to the use of WPF, and I&#8217;m not the first LessThanDot-er that has had [issues with WPF][15] (and yes, my graphics drivers are up to date).

There are times (like with the WordPress install above) when things won&#8217;t go 100% smoothly. I have only been using it for a few weeks and so far the level of issues has not been enough to warrant a rant on twitter or switching to another topic of study, but stay tuned, it&#8217;s still a young technology.

## Go Try It

There is little reason not to try this tool. The [download][16] is free, you don&#8217;t have to invest a lot of time to learn how to use a few options in it, and you might just find that it&#8217;s a useful tool to have in your toolbelt. If you are one of the crowd that&#8217;s convinced you don&#8217;t need to learn anything past the one technology you know (and use WITH NO LOCK everywhere) then this may be too complex for you, but the rest should at least give it a try, even the non-developers. Being able to spend an afternoon putting together even a fake little website can be a fun accomplishment and, for me at least, reminded me why I got into web development and programming in the first place.

 [1]: http://drewby.com/a-wordpress-blog-in-15-minutes-with-webmatrix "A WordPress Blog in 15 Minutes with WebMatrix"
 [2]: http://weblogs.asp.net/scottgu/archive/2010/07/02/introducing-razor.aspx "Razor engine announcement"
 [3]: http://www.asp.net/web-pages "Resources at ASP.Net"
 [4]: http://www.asp.net/webmatrix/tutorials "WebMatrix Tutorials at ASP.net"
 [5]: http://blogs.msdn.com/b/brian_swan/archive/2010/07/12/creating-php-websites-with-webmatrix.aspx "Creating PHP Websites with WebMatrix"
 [6]: /index.php/WebDev/UIDevelopment/AJAX/trying-out-webmatrix-and-razor "Chrissie - Trying Out WebMatrix and Razor"
 [7]: http://erikej.blogspot.com/ "Everything SQL Server Compact"
 [8]: https://xosfaere.wordpress.com/2011/01/30/entity-framework-code-first-development-with-sql-ce-4-0/ "Code-First Development (EF) w/ SQL CE 4.0"
 [9]: http://www.microsoft.com/web/post/webmatrix-reports-workspace-help "Reports Workspace Help"
 [10]: http://www.microsoft.com/web/post/use-webmatrix-to-optimize-your-site-for-search-engines "Use WebMatrix to optimize your site for search engines"
 [11]: http://blogs.iis.net/msdeploy/ "Official WebDeploy team blog"
 [12]: http://www.microsoft.com/web/Hosting/Home? "Find Web hosting at microsoft.com"
 [13]: http://learn.iis.net/page.aspx/871/publish-your-website/ "Publish Your Website - IIS.Net"
 [14]: http://www.hanselman.com/blog/MicrosoftWebMatrixInContextAndDeployingYourFirstSite.aspx "Microsoft WebMatrix in Context and Deploying Your First Site"
 [15]: /index.php/DesktopDev/MSTech/a-few-reasons-why-i-m-not-yet-moving-to "Chrissie's post on A Few Reasons I'm Not Yet Moving to WPF"
 [16]: http://www.microsoft.com/web/webmatrix/ "WebMatrix page at Microsoft"