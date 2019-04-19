---
title: An ode to Log, I love thee so
author: ThatRickGuy
type: post
date: 2009-11-20T14:33:50+00:00
ID: 637
url: /index.php/webdev/webdesigngraphicsstyling/an-ode-to-log-i-love-thee-so/
views:
  - 7898
rp4wp_auto_linked:
  - 1
categories:
  - Web Design, Graphics and Styling
tags:
  - silverlight logging webservices architexture

---
Hey Everyone,

<p style="text-indent: 30pt;">
  Rick here again. I really want to get to the nuts and bolts of KBay, but first, we have to get into some of the tools our shop has in place. We leverage these tools to improve almost all aspects of our development life cycle. Two of the biggest tools we refer to as Environment Provider and Application Logging.
</p>

### The Environment Provider

<p style="text-indent: 30pt;">
  The Environment Provider does exactly what its title suggests: It provides an environment, or more specifically, all of the meta-data an application needs to exist in a specific configuration. Connection strings, end points, application configurations, etc. There is one single database that holds all of this data. This data is identified by a “context”. For example, the KBay webservices can exist on a local developer's PC, a Development Server, a Staging Server, and a Production Server. Each of these locations can connect to either the Development or Production database. So a combination of the location of the application and the desired data environment gives us a context for which a set of environment parameters to use. In the case of KBay, the structure is pretty simple. We have some other apps, international apps with multiple data sources, well, they get a bit crazy, but we have an app just for managing all these settings:
</p>

### The EP Tree

<img src="http://ringdev.com/images/EnvironmentProvider.JPG" alt="Environment Provider Tree" title="Environment Provider Tree" style="margin-left:50px;" />

<p style="text-indent: 30pt;">
  It started off as a proof of concept for organizing Environment Provider data when someone mentioned a “Tree” controller and someone (probably Adam!) put it to a Silverlight challenge. Different types of parameters (leaves) are color coded, and if connection strings are detected that point to databases other than their context (branches) would imply, they turn red. And yes, the sun and sky are also animated. Although it started off as a joke, we actually found some bad configurations with the color-coded leaves.
</p>

### Accessing the Meta-Data

<p style="text-indent: 30pt;">
  All of this data gets exposed through a web interface. A web service that takes a context, User Name, and other external parameters returns the appropriate environment data. So all of our applications go to 1 central point for configuration data.
</p>

### It's Better Than Great, It's Log!

<p style="text-indent: 30pt;">
  The other biggie is the Logging System. The Logging System is pretty straightforward. There is a table that stores application information, one that stores user information (tied in to Active Directory), one that stores Session information (User X started System Y at this date on this machine with this Environment Provider Context), and one table that stores log entries for the sessions.
</p>

### By Your Powers Combined

<p style="text-indent: 30pt;">
  We store the Environment Provider Context on each session. This is actually a function of the Environment Provider. When an application calls the EP web service, in addition to all of the environment data, it also gets a SessionID. The call into the Environment Provider calls the logging service which creates the log session, populates the appropriate data and logs an application start event. This allows us to quickly identify log entries not just to an application, but to a specific user, on a specific server, with a specific data connection, etc...
</p>

### Getting at More Data

<p style="text-indent: 30pt;">
  All of this log data is exposed through a really quick and dirty Silverlight app that provides us with some standard search functionality:
</p>

<img src="http://ringdev.com/images/LogViewer.JPG" alt="Log Viewer" title="Log Viewer" style="margin-left:50px;" />

### Great Business Value (aka: How to convince your boss it's worth doing)

<p style="text-indent: 30pt;">
  Being able to quickly and easily pull up log entries has proven absolutely essential for tracking down user issues, communicating error details between developers, and even determining usage and error rates in applications to help with prioritization. For instance, I give you our Application Usage tool:
</p>

<img src="http://ringdev.com/images/AppUsage.JPG" alt="Application Usage Sidebar" title="Application Usage Sidebar" style="margin-left:25px;" /><img src="http://ringdev.com/images/AppUsageDetail.JPG" alt="Application Usage Sidebar - Single Application View" title="Application Usage Sidebar - Single Application View" style="margin-left:15px;" /><img src="http://ringdev.com/images/AppUsageDrill.JPG" alt="Application Usage Sidebar - Usage By User" title="Application Usage Sidebar - Usage By User" style="margin-left:15px;" />

### Rendered Data at Your Finger Tips

<p style="text-indent: 30pt;">
  Application Usage is a tiny Silverlight App that sits on our developers' Share Point portal. Similar to the lay out here at LessThanDot, we have a right hand column with generalized data displayed. We've found that column to be a great place to add Silverlight apps for interactive reporting. The Application Usage app for instance, and another that tracks Source Control locks, lock durations, and even file names per developer.
</p>

### Where's the Code?

<p style="text-indent: 30pt;">
  That's about it for this post, tomorrow I hope to get more written about the communication system. And after that, some of the code and XAML that makes this stuff tick!
</p>

-Rick