---
title: Using Binding Redirects to Stay Out of DLL Hell
author: Alex Ullrich
type: post
date: 2010-10-25T14:14:00+00:00
excerpt: "We found ourselves in a tricky situation at work this week.  I'm surprised it hadn't come up before, but I suppose our customers aren't always the type to move to a new technology quickly.  But we had a customer trying to install the back end of our app&hellip;"
url: /index.php/webdev/serverprogramming/using-binding-redirects-to-stay-out-of-d/
views:
  - 8517
rp4wp_auto_linked:
  - 1
categories:
  - ASP.NET
  - Server Programming
tags:
  - asp.net
  - wcf

---
We found ourselves in a tricky situation at work this week. I&#8217;m surprised it hadn&#8217;t come up before, but I suppose our customers aren&#8217;t always the type to move to a new technology quickly. But we had a customer trying to install the back end of our application on a Windows 2008 server, and having trouble. We use AzMan to get our application&#8217;s permissions to work with windows authentication, and there was a new version included with server 2008. Our application was compiled against the 2003 version, and it was having problems when installed in this environment.

We first tried updating our references to use the 2008 version, and this seemed to work fine. Until people tried to install the most recent builds on XP and 2003 servers the next day. Needless to say, we wanted to avoid building different installers for different operating systems. The application had been running fine simply using the AzMan version found in the server&#8217;s GAC previously, so we decided to try and find a way to make it work that way.

Eventually we came across the idea of a [Binding Redirect][1] and thought this might be helpful. It seems like the redirect only works when pointing to a newer version of a referenced DLL, so we first changed our references to use the 1.0 version of AzMan (from windows 2000) and kicked of a fresh build of the installer. We set up our redirects like so:

<pre>&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;configuration&gt;
  &lt;configSections&gt;
    &lt;!-- snip --&gt;
  &lt;/configSections&gt;
    &lt;runtime&gt;
        &lt;assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1"&gt;
            &lt;dependentAssembly&gt;
                &lt;assemblyIdentity name="Microsoft.Interop.Security.AzRoles"
                              publicKeyToken="31bf3856ad364e35"
                              culture="neutral" /&gt;
                &lt;bindingRedirect oldVersion="1.0.0.0"
                                                 newVersion="2.0.0.0"/&gt;
                &lt;bindingRedirect oldVersion="1.0.0.0"
                                                 newVersion="1.2.0.0"/&gt;
            &lt;/dependentAssembly&gt;
        &lt;/assemblyBinding&gt;
    &lt;/runtime&gt;
    &lt;!-- snip --&gt;
&lt;configuration&gt;</pre>

At first it didn&#8217;t work, but looking at the error we noticed that the app was looking for the 2.0 version of AzMan (on a 2003 box where we were expecting 1.2). Wondering if the ordering of the redirects matters, we switched the two redirect lines and restarted IIS. Lucky for us, it worked (on Windows 2008 R2, 2008 and 2003 sp2 boxes). 

This isn&#8217;t something I&#8217;ve had to do a lot, and I&#8217;m not sure it&#8217;s something I&#8217;d like to do too often (I may be overly obsessive about local references for a few reasons). But for cases like this where you&#8217;re working with a legacy component that is tightly bound to the OS AND maintains a pretty consistent interface across versions, it can certainly come in handy.

 [1]: http://msdn.microsoft.com/en-us/library/eftw1fys.aspx