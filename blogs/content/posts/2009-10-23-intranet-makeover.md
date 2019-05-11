---
title: 'Intranet Makeover: Virtual Web Sites with Custom DNS Names'
author: Erik
type: post
date: 2009-10-23T13:22:46+00:00
ID: 594
url: /index.php/webdev/serveradmin/intranet-makeover/
views:
  - 6051
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft IIS
  - Server Admin

---
Why make your users interact with URLs such as: [http://companyweb1/mainsite/repository/main.html][1] when they could just type [repository][1]?

## The Mess

Your corporate intranet probably has a lot of different resources on it. Wherever those are, users have to gain access to them somehow. And there are several issues that can reduce ease of access, whether the resources are whole web sites or individual web pages.

If resources are mainly grouped together on one site, then you have to deal with problems like:

  * A possible unfriendly server name that people have to remember or bookmark.
  * Paths and subpaths and page names.
  * Navigating to a single launch page, then searching for or having to click through to the desired resource.
  * The more resources there are the more bookmarks there have to be or the longer the list of items.
  * Conflicting page names prevent you from using the default content page for some destinations.

There are additional problems, whether your resources are mostly on one site or not:

  * A server moves or needs its name changed, so everyone has to update their URLs or change their process to find what they need.
  * You want to combine web sites from two servers to one, or split them apart from one server to two.

But there is a very simple and easy to implement idea that can help all this.

## The Solution

Use virtual web sites with custom DNS names!

Say you have a web site that everyone knows as the document repository. Choose a cool, short name for it that people will remember and isn't hard to type. How about "repository"? Great, then. In your DNS, add "repository" as a CNAME for the current DNS name that everyone's using (say, companyweb1). It literally takes a few seconds for a DNS admin to add an entry like this.

Then, run your sites—wherever they are—using virtual hosting with the new DNS name.

The finishing touch is to set the default content page correctly so the trailing "main.html" in the URL can be eliminated. Since it's now its own site, you shouldn't have conflicts with other sites.

If you were previously using a virtual folder, you just make the new site's root document the same folder. Some testing is a good idea because the site needs to function properly in its new location, especially links needing to use the correct name and path (but I personally think that a well-designed site could probably move over with no changes). You can actually run both the new and old access methods at the same time to do testing and ensure a smooth transition.

Now getting to the resource is much more simple. People can visit your site by launching their browser and typing "repository" in the address. That's it! Nothing else is needed. They're in the site they want.

## IIS 6.0 Virtual Hosting Instructions

You can set up virtual hosting at site setup time, or convert to using it later.

At site setup time, when running the Web Site Creation Wizard, on the IP Address and Port Settings wizard page, put the desired DNS name in the "Host header for this Web site" box.

To modify an existing site, get properties on it, then go to Web Site -> IP Address Advanced -> select an item in the list and Edit it -> Host Header value.

Virtual hosting for other web servers or IIS versions is undoubtedly easy, you just have to look up how to do it.

## A Couple More Tricks

Here are some additional ways to use virtual sites and custom DNS names.

<li style="margin-bottom:1em;">
  For all my web sites now, I put all the database connection strings for all my environments (prod/test/dev/local/etc) in a file within the site. Then the web application reads that file and uses the host header name to figure out which connection string to use. So after copying my test site to production I don't have to change a thing! The production site connects to the production database because it was reached with the production name.
</li>
<li style="margin-bottom:1em;">
  It becomes so easy to set up those additional environments, as well. "repositoryt" or "repdev" can even be run on the same web server as the production site. Or somewhere else. No user has to know or care about that stuff.
</li>
  * As long as you can set up the virtual hosting for the web server, you can get the site working without a DNS entry by adding the desired name to your hosts file (in Windows anyway). You can pick a completely new name, or use an existing DNS name to override it so only your computer sees a different site than others. Maybe you want to test the new site, or maybe you just want a "secret" site accessible only by someone using your computer (until someone finds the site on the web server and decides to see what it is).

## One Gotcha

The one caveat for you is that if you are using Kerberos integrated authentication and doing delegation, where your web site is connecting over the **network** as the windows user visiting the web site, then you have to do some extra work with the [SetSPN Utility][2] so that the new DNS name gains the permissions of the server (under its real name).

However, if you're using NTLM integrated authentication and doing impersonation, where your web site is only accessing resources on the **local** web server as the visiting windows user, then you should have no problems.

I hope this tip is useful to you.

Erik

 [1]: /
 [2]: http://www.microsoft.com/downloads/details.aspx?FamilyID=5fd831fd-ab77-46a3-9cfe-ff01d29e5c46&DisplayLang=en