---
title: ASP.Net WebAPI – Controlling Response Formats
author: Eli Weinstock-Herman (tarwn)
type: post
date: -001-11-30T00:00:00+00:00
excerpt: 'Recently we were trying to consume a WebAPI call from an application that could only really consume HTML tables and basic XML. Unfortunately, nowhere in the long list of content-types in the Accepts header does the application actually list XML. So WebA&hellip;'
draft: true
url: /?p=1985
categories:
  - ASP.NET
tags:
  - asp.net webapi
  - json
  - xml

---
Recently we were trying to consume a WebAPI call from an application that could only really consume HTML tables and basic XML. Unfortunately, nowhere in the long list of content-types in the Accepts header does the application actually list XML. So WebAPI would dutifully go through the list of types, checking them with the available formatters, before finally giving up and using the default formatter. Which of course is JSON, which the app couldn&#8217;t figure out.

There&#8217;s two ways to resolve issues like this, at the global level or at the individual action level.

Sample code for both of the methods below is on github: [tarwn/Blog_WebApiFormatters][1]

## Globally Change Default Formatter

Altering the global list of formatters for WebAPI is easy. In the situation above, all we need to do is make sure that the XML Formatter is the one at the top of the list, like so:

**Global.asax**

<pre>protected void Application_Start()
{
	// ...

	var xml = GlobalConfiguration.Configuration.Formatters.XmlFormatter;
	GlobalConfiguration.Configuration.Formatters.Remove(xml);
	GlobalConfiguration.Configuration.Formatters.Insert(0, xml);

	// ...
}</pre>

**Sample Action &#8211; no Extra Code**

<pre>[HttpGet]
public List&lt;SampleObject&gt; GiveMeData()
{
	return _repository.GetData();
}</pre>

Now when we don&#8217;t find a match for the supplied Accept headers, the XmlFormatter is the first one at bat. Occasionally I would still receive JSON, when the XmlFormatter couldn&#8217;t serialize something, but that&#8217;s why we test our code before releasing.

Critical Note: This method will only work if the requester specifies text/anything or application/anything in the Accepts header. \*/\* and empty values do not work and will instead respond with JSON. I&#8217;ve been digging into the source because this behavior doesn&#8217;t seem correct, but haven&#8217;t found an answer in the source code yet. 

## Change Formatter at the Action level

What if we don&#8217;t want to change the defaults globally just to ensure a couple return XML? By returning a HttpResponseMessage object, we can set which formatter we would like to be used directly. 

**Action with Explicit Formatter**

<pre>[HttpGet]
public HttpResponseMessage GiveMeDataInJSON()
{
	var data = _repository.GetData();

	return Request.CreateResponse&lt;List&lt;SampleObject&gt;&gt;(HttpStatusCode.OK, data, new JsonMediaTypeFormatter());
}</pre>

And there we go, XML on demand every time.

## Wrapup

This works just as well with custom formatters, but the difference in matching behavior between the JSON and Xml Formatters is worrisome for the global solution. The project I outlined above has a sample index page with some jQuery test code you can play with. Changing the Accepts header in the index.html page before running will let you see the behavior in person.

 [1]: https://github.com/tarwn/Blog_WebApiFormatters