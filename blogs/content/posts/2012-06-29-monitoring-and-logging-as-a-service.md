---
title: Monitoring and Logging as a Service – Introduction
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2012-06-29T10:03:00+00:00
excerpt: "Monitoring [in IT] sucks and I am probably more critical of its state than most IT people. Over the next few posts I'm going to integrate a sample application with several logging and data services, evaluating them against my own needs and expectations. Besides the comparison, and perhaps more importantly, the examples will show how easy it is to instrument our applications and start getting visibility into what is actually happening behind the scenes."
url: /index.php/enterprisedev/instrumentation/monitoring-and-logging-as-a-service/
views:
  - 16454
rp4wp_auto_linked:
  - 1
categories:
  - Instrumentation
tags:
  - logging
  - monitoring
  - operations

---
[Monitoring [in IT] sucks][1] and I am probably more critical of its state than most IT people. Over the next few posts I&#8217;m going to integrate a sample application with several logging and data services, evaluating them against my own needs and expectations. Besides the comparison, and perhaps more importantly, the examples will show how easy it is to instrument our applications and start getting visibility into what is actually happening behind the scenes.

## Monitoring in IT is Behind

Ten years ago I regularly worked with monitoring systems that recorded thousands of data points per second. They ran on beige desktop systems shoved in network closets, dell towers that were beginning to leave the beige behind, and 2U and 4U servers. They communicated through 1/10 switch ports, often fixed at 1 Mbps. They had to understand 10&#8217;s to 100&#8217;s of proprietary data protocols. They ran on IDE drives, storing years worth of data despite the drives barely pushing 100GB.

And yet they were recording thousands of data points per second, in real time, and storing it for historical analysis. They drove highly customizable interfaces supporting complex animations, graphs, meters, drag and drop design, and custom controls. Statistical analysis tools were available to analyze data from a dynamic number of sources across a user defined number of metrics in real time. Alerting systems provided email, SMS, and pager alerts while monitoring fixed alarm values, statistical anomalies, and trend-based alerting that alerted based on the projected state. Then there were the external applications, APIs, excel plugins, &#8230; Ten years ago.

This was the state of monitoring in the manufacturing world a decade ago, running on hardware and operating systems less capable than most of the cellphones we carry today. 

## But IT Demand is Continuing to Grow

In IT, the most mature market for monitoring and data collection is infrastructure (systems and network). But &#8220;most mature&#8221; is [still behind][1]. The fact that so many systems barely have the concept of recipes for common monitoring targets, or lock our data behind such limited interfaces, leaves most of the commercial options closer to 15 years behind. 

Software development lags even farther behind. 

The current state of monitoring for most of the application world hovers between nonexistent and barely proactive. I have seen as many applications lacking even basic error logging as I have those with it, and the presence of even regular daily or weekly state checks has been even less prevalent. The state of application monitoring can be summed up as &#8220;We usually know when the system breaks, sometimes we get those messages immediately, and sometimes we actually read them&#8221;. 

This market is beginning to emerge more, and hopefully that movement will start driving more tool development. Somewhere between fixed value monitoring and reporting on unstructured log data, we are building more businesses that value transparency in our application behavior. And even though most of the tools are better suited to infrastructure or basic log parsing, there is still an enormous potential in that first level of visibility beyond logging to a local file or an email address.

## Tools to be Reviewed

Over the next couple posts, I&#8217;ll introduce some sample code that integrates an application with several logging services. The application to be instrumented is an ASP.net MVC 3 application that has a very basic view with links to a short and longer running action. Most of the services to be reviewed are built around managing unstructured log data, which isn&#8217;t a perfect fit, but does give us a lot of capability very cheaply.

The goal is to capture data consistently with as little impact to the code and performance of the original application as possible. Once data has been captured we then want to make it usable, creating some useful searches and visualizations to generate value from that data. 

As a secondary goal, I hope the simplicity of the example and integration code will inspire more developers to start adding transparency to their code, not just for the benefit it will bring them, but also to put more pressure on tooling to improve.

 [1]: http://lusislog.blogspot.com/2011/06/why-monitoring-sucks.html "Why Monitoring Sucks"