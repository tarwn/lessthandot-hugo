---
title: ‘LocalSqlServer’ Error Deploying WebSecurity in WebMatrix/Web Pages
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2011-08-09T10:52:00+00:00
excerpt: "Several weeks ago I was working on a sample site in Web Matrix and ran into a brick wall when I attempted to implement WebSecurity against my website. It seemed no matter what I tried I was getting errors about my database, errors about a database I didn't know about, errors trying to deploy the config file at all."
url: /index.php/webdev/serverprogramming/localsqlserver-error-deploying-websecurity/
views:
  - 4693
rp4wp_auto_linked:
  - 1
categories:
  - Server Programming
tags:
  - error
  - razor
  - web pages
  - webmatrix
  - websecurity

---
Several weeks ago I was working on a sample site in Web Matrix and ran into a brick wall when I attempted to implement WebSecurity against my website. It seemed no matter what I tried I was getting errors about my database, errors about a database I didn&#8217;t know about, errors trying to deploy the config file at all. After hours of debugging and random attempts at web.config hackery, I finally tried the basic starter sites and found out that those, too, suffered from problems when you attempt to deploy them.

> **Configuration Error**
  
> **Description:** An error occurred during the processing of a configuration file required to service this request. Please review the specific error details below and modify your configuration file appropriately.
> 
> **Parser Error Message:** The connection name &#8216;LocalSqlServer&#8217; was not found in the applications configuration or the connection string is empty. 

Today I managed to solve it based on an [archived forum post][1] I found through google. 

The solution is to add the following section to your web.config:

<pre><!-- Added in an attempt to make simple security work --&gt;
<connectionStrings&gt;
	<remove name="LocalSqlServer" /&gt;
	<add name="LocalSqlServer" connectionString="Data Source=.App_DataMyJunk.sdf" providerName="System.Data.SqlServerCe.4.0" /&gt;
</connectionStrings&gt;
<appSettings&gt;
	<add key="enableSimpleMembership" value="true" /&gt;
</appSettings&gt;</pre>

Basically what is happening is that my web host has a machine.config with a SQL Connection string called &#8220;LocalSqlServer&#8221; (it&#8217;s created by default by the .Net framework). For some reason when we use anything Membership related, it attempts to access the configured connection string for Membership, even though WebSecurity is provided it&#8217;s own connection string on initialization.

So the solution is to remove the settings that has been applied to our site from the host&#8217;s machine.config and apply a new one. From what I can tell, it doesn&#8217;t seem to matter if the new connection string is accurate, only that it exists.

Yep, this means I don&#8217;t actually have a SQLCE database named, MyJunk. I can tell you&#8217;re disappointed. Also I don&#8217;t know if ./ would work in this path, so please don&#8217;t use this as a how-to guide on how to create a working SQLCE connection string.

 [1]: http://forum.winhost.com/archive/index.php/t-8697.html