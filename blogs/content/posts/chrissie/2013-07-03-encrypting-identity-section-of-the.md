---
title: Encrypting identity section of the web.config for a Nancy website
author: Christiaan Baes (chrissie1)
type: post
date: 2013-07-03T08:28:00+00:00
ID: 2128
excerpt: Encrypting the identity username and password in ASP.Net using Nancy.
url: /index.php/webdev/serverprogramming/encrypting-identity-section-of-the/
views:
  - 14866
categories:
  - ASP.NET
  - Server Programming
tags:
  - nancy
  - web.config

---
On Monday the 3 monthly password change happened and the account I use for testing this application also had it&#8217;s password changed. Oh joy. 

This is an app that uses windows authentication but my test server has an identity impersonate to make sure it always uses the same account. Which is not the account I use to develop on my machine. 

Anyway. I noticed that the password was wrong because of this.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/nancy/WebconfigEncrypt.png?mtime=1372839465"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/nancy/WebconfigEncrypt.png?mtime=1372839465" width="1007" height="405" /></a>
</div>

Yay, it shows my username and password in plaintext. 

It is to note that this also happens before Nancy even get&#8217;s in to the game. 

So I would like to encrypt that section.

I could use <code class="codespan">aspnet_Regiis -pef system.web/identity pathtowebsite</code> to get around that. But then I guess I would have to do that everytime that password changes. And I&#8217;m to lazy for that. 

But I can also do that on Application startup in the Global.Asax. To make sure it is encrypted every time the application started. 

So in the Gloabl.asax in the Application_Start method I put this.

```vbnet
Log.Debug("Rootpath = {0}", "")
        Dim config = WebConfigurationManager.OpenWebConfiguration("")
        Dim section = config.GetSection("system.web/identity")
        Log.Debug("Section = {0}", section.ToString)
        If section IsNot Nothing AndAlso Not section.SectionInformation.IsProtected Then
            section.SectionInformation.ProtectSection("RSAProtectedConfigurationProvider")
            config.Save()
        End If```
And now I get this errormessage.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/nancy/WebconfigEncrypt2.png?mtime=1372839980"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/nancy/WebconfigEncrypt2.png?mtime=1372839980" width="1020" height="431" /></a>
</div>

So there, it works and I&#8217;m happy, let&#8217;s move on.