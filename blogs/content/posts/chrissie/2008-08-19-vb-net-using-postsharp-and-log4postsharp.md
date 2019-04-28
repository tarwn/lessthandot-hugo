---
title: 'VB.Net: Using PostSharp and Log4PostSharp'
author: Christiaan Baes (chrissie1)
type: post
date: 2008-08-19T09:13:22+00:00
ID: 110
url: /index.php/desktopdev/mstech/vb-net-using-postsharp-and-log4postsharp/
views:
  - 5090
categories:
  - Microsoft Technologies
tags:
  - log4net
  - log4postsharp
  - postsharp
  - vb.net

---
After reading an [article on the codeproject][1] about PostSharp and Log4Net.

So I tried to replicate that in VB.Net and hit a bump in the road. But here is how it got solved in the end.

So like the instructions say I downloaded [PostSharp][2] and installed it. I also downloaded the [log4postsharp sourcecode][3].

Then I created a consoleapplication.
  
I set the references Log4PostSharp.dll and PostSharp.Public.dll (you can find this library on the &#8220;.NET&#8221; tab of the &#8220;Add reference&#8230;&#8221; dialog). And a reference to the log4net dll.

I then added some code to the module.

```vbnet
Imports Log4PostSharp

Module Module1

    Sub Main()
        Console.WriteLine(Add(1, 1))
        Console.ReadLine()
        Dim sum As Integer
        sum = Add(1, 2)
        Console.WriteLine(Add(1, 1))
        Console.ReadLine()
    End Sub

    &lt;Log(EntryLevel:=LogLevel.Debug, EntryText:="Adding {@i1} to {@i2}.", ExitLevel:=LogLevel.Debug, ExitText:="Result of addition is {returnvalue}.")&gt; _
  Private Function Add(ByVal i1 As Integer, ByVal i2 As Integer) As Integer
        Return i1 + i2
    End Function

End Module
```
Then I added an app.config file.
  
And added these log4net configurationsetting to it.

```xml
&lt;?xml version="1.0" encoding="utf-8" ?&gt;
&lt;configuration&gt;
  &lt;configSections&gt;
    &lt;section name="log4net" type="log4net.Config.Log4NetConfigurationSectionHandler, log4net" /&gt;
  &lt;/configSections&gt;
  &lt;log4net&gt;
    &lt;appender name="MainAppender" type="log4net.Appender.ConsoleAppender"&gt;
      &lt;layout type="log4net.Layout.PatternLayout"&gt;
        &lt;conversionPattern value="%date [%thread] %-5level %logger %ndc - %message%newline" /&gt;
      &lt;/layout&gt;
    &lt;/appender&gt;
    &lt;root&gt;
      &lt;level value="ALL" /&gt;
      &lt;appender-ref ref="MainAppender" /&gt;
    &lt;/root&gt;
  &lt;/log4net&gt;
&lt;/configuration&gt;
```
But that did nothing, in the logging sense anyway. 

So to make sure I didn&#8217;t make a mistake in the log4net configuration, I added this to the main method.

```vbnet
log4net.Config.BasicConfigurator.Configure()
        Dim log As log4net.ILog = log4net.LogManager.GetLogger("logger-name")
        log.Debug("test")
        Console.ReadLine()
        ```
and that made the log line for me. Just the one, not the one from log4postsharp.

So I posted a question on the [postsharp forum][4]. And I got a reply the same day.

So I added this to the AssemblyInfo.vb

```xml
&lt;Assembly: XmlConfigurator(Watch:=True)&gt; 
```
And then I removed these lines again

```vbnet
log4net.Config.BasicConfigurator.Configure()
        Dim log As log4net.ILog = log4net.LogManager.GetLogger("logger-name")
        log.Debug("test")
        Console.ReadLine()
        ```
and everything works as expected.

 [1]: http://www.codeproject.com/KB/dotnet/log4postsharp-intro.aspx?display=Print
 [2]: http://www.postsharp.org
 [3]: http://code.google.com/p/postsharp-user-plugins/wiki/Log4PostSharp
 [4]: http://www.postsharp.org/forum/post1855.html#p1855