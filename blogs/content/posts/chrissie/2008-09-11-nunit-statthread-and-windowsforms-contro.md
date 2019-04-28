---
title: 'Nunit: StatThread and WindowsForms Controls'
author: Christiaan Baes (chrissie1)
type: post
date: 2008-09-11T06:40:49+00:00
ID: 136
url: /index.php/desktopdev/mstech/nunit-statthread-and-windowsforms-contro/
views:
  - 14819
categories:
  - Microsoft Technologies
tags:
  - .net
  - nunit
  - statthread
  - windows forms

---
When you try to instantiate a windowsforms control in your NUnit test, you could get the following error:

> System.Threading.ThreadStateException: Current thread must be set to single thread apartment (STA) mode before OLE calls can be made. Ensure that your Main function has STAThreadAttribute marked on it.

This has something to do with the fact that windows forms controls like to run in STA (Single threaded apartment) and the latests version of Nunit run as MTA (Multi Threaded apartment).

After some googling (which only returned a small number of hits), I found [&#8220;Nunit and STATThread by frater&#8221;][1]. The solution happens to be very simple. Add this to the assembly where you have the test and the error will go away.

```xml
&lt;configSections&gt;
    &lt;sectionGroup name="NUnit"&gt;
      &lt;section name="TestRunner" type="System.Configuration.NameValueSectionHandler"/&gt;
    &lt;/sectionGroup&gt;
  &lt;/configSections&gt;

  &lt;NUnit&gt;
    &lt;TestRunner&gt;
      &lt;add key="ApartmentState" value="STA" /&gt;
    &lt;/TestRunner&gt;
  &lt;/NUnit&gt;```
So another day and another problem solved.

 [1]: http://frater.wordpress.com/