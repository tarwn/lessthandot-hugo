---
title: Twisting My Arm – How I was Persuaded to Change StructureMap Versions
author: Alex Ullrich
type: post
date: 2010-06-02T10:00:00+00:00
url: /index.php/webdev/serverprogramming/my-compelling-reason-for-upgrading-struc/
views:
  - 6201
rp4wp_auto_linked:
  - 1
categories:
  - ASP.NET
  - Server Programming
tags:
  - structuremap

---
I&#8217;ve been using StructureMap 2.5x for some time, and I have been quite pleased with it. I&#8217;d read a bit about 2.6x, and the improvements to the registry DSL seemed cool but not quite cool enough to give me the motivation I needed to make upgrading the version used in my project a priority.

A week or two ago, I found what proved to be my compelling reason. It&#8217;s a method called

<pre>ObjectFactory.ReleaseAndDisposeAllHttpScopedObjects();</pre>

And it does just what it says it does. This could really help in one particular situation encountered in my application (using StructureMap for NHibernate session management in a web context). I need to cache the session factory as a singleton, but the sessions themselves should be unique _per request_. Before coming across this method I was using a filter to clean up sessions at the end of the request:

<pre>public class SessionPerRequest : ActionFilterAttribute
{
	public override void OnActionExecuted (ActionExecutedContext filterContext)
	{
		// for when session doesn't need to remain open for view rendering
		//StructureMap.ObjectFactory.GetInstance&lt;NHibernate.ISession&gt;().Dispose();
	}

	public override void OnResultExecuted (ResultExecutedContext filterContext)
	{
		StructureMap.ObjectFactory.GetInstance&lt;NHibernate.ISession&gt;().Dispose();
	}
}</pre>

This was working fine, but I did need to decorate every controller method where I wanted to apply it. In addition to not really being my idea of a good time, this leaves the door open for me to forget to decorate a method somwhere, causing the app to leak sessions all over the place. Also, if I put it someplace it&#8217;s not needed, a session will be created only to be disposed of!

So today, I finally got around to upgrading. I made the slight improvements to Registry code that were now possible, but I was most excited to get rid of the filter above, and replace it with this method (in Global.asax.cs):

<pre>protected void Application_EndRequest()
{
    ObjectFactory.ReleaseAndDisposeAllHttpScopedObjects();
}</pre>

While there was certainly some pain involved in removing the filter I had used previously, this approach will make my life much easier in the long run. I already trust StructureMap to cache these different instances for me correctly, why not trust it to clean up afterwards as well? It&#8217;s also nice to know that if I end up needing any other objects to be unique per http context, I won&#8217;t need to write any more code to clean up after myself. This is a great feature added to the library, and I wish I&#8217;d read about it sooner!

**EDIT:** I saw a thread today mentioning another method called 

<pre>HttpContextBuildPolicy.DisposeAndClearAll();</pre>

that&#8217;s been available since StructureMap 2.3.5 that seems to do the exact same thing. So I didn&#8217;t really need to upgrade after all, but I guess its nice to stay current. I don&#8217;t want to make excuses for my ignorance, but I&#8217;ve gotta say accessing it through the ObjectFactory makes a bit more sense to me. In a way I&#8217;m more impressed that it showed up there, because its&#8217; often a harder decision to relocate code when refactoring than to add it in the first place!