---
title: nhibernate, nunit 2.4.6 and log4net
author: Christiaan Baes (chrissie1)
type: post
date: 2009-05-06T09:46:01+00:00
ID: 412
url: /index.php/desktopdev/mstech/nhibernate-nunit-2-4-6-and-log4net/
views:
  - 7090
categories:
  - Microsoft Technologies
tags:
  - log4net
  - nhibernate
  - nunit

---
Today I noticed that my integration tests with nhibernate were runnning very slow on the build server. Very slow meaning 1 hour to run while they run for 6 minutes on my dev machine all 840 of them. Something was different.

I am using Finalbuilder 5.5 on my build machine and that uses the nunit console to do the dirty work for it. 

So I tried running it in the nuint gui tool and there too it was slow.
  
So I clicked a bit on the tabs, one never knows when one might get lucky and low and behold I was lucky. The Log tab of the GUi builder was getting lots of work.
  
I know my readers (all 3 of you) prefer pictures over words so here is a picture.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/nunitconsole.JPG" alt="" title="" width="600" height="353" />
</div>

Apparently nunit 2.4.6 has a config somewhere that says log4net should use debug level logging. Which slows down nhibernate like never before.

So I copy pasted my regular log4net nhibernate appender and other config things into the app.config of the test project 

```xml
&lt;configSections&gt;
      &lt;section name="log4net" type="log4net.Config.Log4NetConfigurationSectionHandler,log4net" /&gt;
    &lt;/configSections&gt;
 
  &lt;!-- This section contains the log4net configuration settings --&gt;
  &lt;log4net debug="false"&gt;

       &lt;appender name="rollingFile" type="log4net.Appender.RollingFileAppender,log4net" &gt;

      &lt;param name="File" value="nHibernate.log" /&gt;
      &lt;param name="AppendToFile" value="true" /&gt;
      &lt;param name="RollingStyle" value="Date" /&gt;
      &lt;param name="DatePattern" value="yyyy.MM.dd" /&gt;
      &lt;param name="StaticLogFileName" value="true" /&gt;
      &lt;param name="maxSizeRollBackups" value="10" /&gt;
         
      &lt;layout type="log4net.Layout.PatternLayout,log4net"&gt;
        &lt;param name="ConversionPattern" value="%d [%t] %-5p %c - %m%n" /&gt;
      &lt;/layout&gt;
    &lt;/appender&gt;

    &lt;!-- Setup the root category, add the appenders and set the default priority --&gt;

    &lt;root&gt;
      &lt;level value="Info"/&gt;
      &lt;appender-ref ref="rollingFile"/&gt;
    &lt;/root&gt;

    &lt;logger name="NHibernate"&gt;
      &lt;level value="Warn" /&gt;
      &lt;appender-ref ref="rollingFile"/&gt;
    &lt;/logger&gt;

    &lt;logger name="NHibernate.Loader.Loader"&gt;
      &lt;level value="Warn" /&gt;
      &lt;appender-ref ref="rollingFile"/&gt;
    &lt;/logger&gt;

  &lt;/log4net&gt;```
and added this to the first test, just to check if it was faster. 

```vbnet
log4net.Config.XmlConfigurator.Configure()```
And now my integration tests are back to their normal slow self.

I think this problem will not occur in nunit 2.5 and above since they now use a different logger then log4net.