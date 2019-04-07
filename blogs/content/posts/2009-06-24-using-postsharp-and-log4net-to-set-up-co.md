---
title: Using PostSharp and log4net to Set Up Controller Logging in ASP.net MVC
author: Alex Ullrich
type: post
date: 2009-06-24T09:45:45+00:00
ID: 409
url: /index.php/webdev/serverprogramming/using-postsharp-and-log4net-to-set-up-co/
views:
  - 38361
rp4wp_auto_linked:
  - 1
categories:
  - ASP.NET
  - Server Programming

---
A friend of mine pointed me in the direction of a cool library for Aspect Oriented Programming called [PostSharp][1] the other day. I&#8217;d read about AOP in the past, but always had so much going on that it slipped my mind to look further into it. But, his excitement rubbed off on me and it wasn&#8217;t long before I thought of a way I could use it. I&#8217;d been wanting to set up logging on a site I&#8217;ve been working on so that I could collect data for a while and report on it to see which controller methods are running the slowest. Not a permanent solution, just a way to gather data for a while to analyze it and identify any pain points I may have missed. Using PostSharp I can do it without littering too much logging code throughout the controllers themselves (all they would need is an attribute).

First thing we need to do is create a table to log to. Something like this:

```mysql
CREATE TABLE  `my_site`.`log` (
  `ID` int(10) unsigned NOT NULL auto_increment,
  `Date` datetime NOT NULL,
  `Thread` varchar(32) NOT NULL,
  `Context` varchar(10) NOT NULL,
  `Level` varchar(10) NOT NULL,
  `Logger` varchar(512) NOT NULL,
  `Method` varchar(200) default NULL,
  `Parameters` varchar(8000) default NULL,
  `Message` varchar(1000) NOT NULL,
  `Exception` varchar(4000) default NULL,
  `ExecutionTime` decimal(14,4) default NULL,
  PRIMARY KEY  (`ID`),
  INDEX `ix_log_level` (`Level`),
  INDEX `ix_log_executiontime` (`ExecutionTime`)
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
```

The only interesting thing here is the &#8220;ExecutionTime&#8221; column. I added this because in this case I am logging to MySQL, and MySQL doesn&#8217;t store the millisecond portion of Date/Times. Seems it would be easier anyway to just log the time rather than try to connect start and finish entries (you could also do it in a single entry, as shown [here][2]). The reason I didn&#8217;t do this is because I wanted to be able to split the table into 3 (start, finish, and exception entries) to get as good an idea as I could what is happening at any given time. 

Another thing to note here is that I added a column for parameter name/value combinations and the method name. log4net has a built-in conversion pattern for determining the method name, but it will not work for me because I plan to wrap log4net in a separate static helper class, in case I ever want to change the logging solution behind the scenes. I also read on log4net&#8217;s [Pattern Layout][3] docs that getting any kind of information about the caller from log4net is very slow, because it generates a call stack to read the information from. That is one hell of a warning, they might as well just put police tape around those methods. So I will take my chances getting this info from PostSharp!

Next is to configure log4net. Added an xml file called log4net.config to the top-level directory in the project. Something like this ought to do:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<log4net>
  <!-- How to set up secondary appender (bufferless) for Exceptions only? -->
  <!-- Log4Net Appender Settings-->
  <root>
    <level value="All" />
    <appender-ref ref="ADONetAppender" />
  </root>
  <appender name="ADONetAppender" type="log4net.Appender.ADONetAppender">
    <bufferSize value="10"/>
    <lossy value="false"/>
    <connectionType value="MySql.Data.MySqlClient.MySqlConnection, MySql.Data"/>
    <connectionString value="Server=999.99.443.206;Database=my_site; Uid=someuser;Pwd=welcome1;"/>
    <commandText value="INSERT INTO Log (Date,Thread,Level,Logger,Message,Method,Parameters,Exception,Context,ExecutionTime) VALUES (?log_date, ?thread, ?log_level, ?logger, ?message, ?method_name, ?parameters, ?exception, ?context, ?execution_time)"/>
    <parameter>
      <parameterName value="log_date"/>
      <dbType value="DateTime"/>
      <layout type="log4net.Layout.RawTimeStampLayout"/>
    </parameter>
    <parameter>
      <parameterName value="thread"/>
      <dbType value="String"/>
      <size value="32"/>
      <layout type="log4net.Layout.PatternLayout">
        <conversionPattern value="%t"/>
      </layout>
    </parameter>
    <parameter>
      <parameterName value="log_level"/>
      <dbType value="String"/>
      <size value="512"/>
      <layout type="log4net.Layout.PatternLayout">
        <conversionPattern value="%p"/>
      </layout>
    </parameter>
    <parameter>
      <parameterName value="context"/>
      <dbType value="String"/>
      <size value="512"/>
      <layout type="log4net.Layout.PatternLayout">
        <conversionPattern value="%x"/>
      </layout>
    </parameter>
    <parameter>
      <parameterName value="logger"/>
      <dbType value="String"/>
      <size value="512"/>
      <layout type="log4net.Layout.PatternLayout">
        <conversionPattern value="%c"/>
      </layout>
    </parameter>
    <parameter>
      <parameterName value="message"/>
      <dbType value="String"/>
      <size value="1000"/>
      <layout type="log4net.Layout.PatternLayout">
        <conversionPattern value="%m"/>
      </layout>
    </parameter>
    <parameter>
      <parameterName value="exception"/>
      <dbType value="String"/>
      <size value="4000"/>
      <layout type="log4net.Layout.ExceptionLayout"/>
    </parameter>
    <parameter>
      <parameterName value="method_name"/>
      <dbType value="String"/>
      <size value="200"/>
      <layout type="log4net.Layout.PatternLayout">
        <conversionPattern value="%property{method_name}"/>
      </layout>
    </parameter>
    <parameter>
      <parameterName value="execution_time"/>
      <dbType value="Decimal"/>
      <layout type="log4net.Layout.PatternLayout">
        <conversionPattern value="%property{execution_time}"/>
      </layout>
    </parameter>
    <parameter>
      <parameterName value="parameters"/>
      <dbType value="String"/>
      <layout type="log4net.Layout.PatternLayout">
        <conversionPattern value="%property{parameters}"/>
      </layout>
    </parameter>
  </appender>
</log4net>
```

Nothing really special there, except for the additional parameters we added that I didn&#8217;t see on most of the vanilla demos. One thing to note is the conversionPattern we used for the custom properties, &#8220;%property{PROPERTY_NAME}&#8221; as it can be very handy if you want to set custom parameters. There&#8217;s also a special &#8220;ErrorLog&#8221; that writes to a flat file without using a buffer, for errors only. This is so that if there is a fatal error in the application, the exceptions leading up to it are not lost. Onward. Next thing we need to do is ensure that log4net is configured when we start up the application. There are two ways to do this:

I first used AssemblyInfo.cs like so:

```csharp
[assembly: log4net.Config.XmlConfigurator(ConfigFile = "log4net.config", Watch = true)]
```
But found I could also use the Application_start method like this:

```csharp
protected void Application_Start()
{
    RegisterRoutes(RouteTable.Routes);
    log4net.Config.XmlConfigurator.Configure(new System.IO.FileInfo(HttpContext.Current.Server.MapPath("log4net.config")));
}
```

I have not yet decided which I like better, but I lean towards the second method because the first just feels a bit dirty. So feel free to pick the one you like best.

Now that this is done, the fun can begin. First thing I did was set up a little helper class, so that I don&#8217;t have calls to log4net all over the place. Besides returning a log4net.ILog to be used in writing entries, this class will have a few methods to use to Add/Remove parameters from the ThreadContext&#8217;s Properties and NDC Stack. This class will look like this:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using log4net;

[System.Diagnostics.DebuggerStepThrough]
public static class Logging
{
    /// <summary>Log an Informational Message</summary>
    public static void Info(String message)
    {
        LogManager.GetLogger("root").Info(message);
    }

    /// <summary>Log an Error Message</summary>
    public static void Error(String message, Exception ex)
    {
        //for errors, use the ErrorLog that will write to a flat file (bufferless) rather than database (buffered)
        LogManager.GetLogger("ErrorLogger").Error(message, ex);
    }

    /// <summary>Insert a parameter to MDC</summary>
    public static void SetParameter(String name, String value)
    {
        ThreadContext.Properties[name] = value;
    }

    /// <summary>Remove parameter from MDC</summary>
    public static void RemoveParameter(String name)
    {
        ThreadContext.Properties.Remove(name);
    }

    /// <summary>Add another context to NDC</summary>
    public static void PushContext(Object obj)
    {
        ThreadContext.Stacks["NDC"].Push(obj.ToString());
    }

    /// <summary>Clear all contexts from NDC</summary>
    public static void ClearContext()
    {
        ThreadContext.Stacks["NDC"].Clear();
    }

    /// <summary>Remove top context from NDC</summary>
    public static void PopContext()
    {
        ThreadContext.Stacks["NDC"].Pop();
    }

    /// <summary>Set Execution time parameter (null for zero)</summary>
    public static void SetExecutionTime(DateTime? current)
    {
        Double execution_time_milliseconds = 0;
        if (current != null)
        {
            DateTime start;
            if (DateTime.TryParse(ThreadContext.Properties["start_time"].ToString(), out start))
            {
                Int64 execution_time_ticks = (current.Value - start).Ticks;
                execution_time_milliseconds = (execution_time_ticks * 1.00 / TimeSpan.TicksPerMillisecond);
            }
        }
        ThreadContext.Properties["execution_time"] = execution_time_milliseconds;
    }

    /// <summary>Store context-specific start time for sharing across methods (within a thread)</summary>
    public static void SetStartTime()
    {
        ThreadContext.Properties["start_time"] = DateTime.Now.ToString();
    }
}
```

Ok so now we know how we are going to do the logging. Now, time to go through and add calls to this logging code throughout our application right? Not exactly. Lets take a look how post sharp comes in. We&#8217;ll want to extend the class &#8220;OnMethodBoundaryAspect&#8221; found in PostSharp.Laos. Use of this class will allow us to weave code into our application at compile time that will execute at various points during method execution (if the class has been tagged with the attribute we are about to create). For this exercise we are concerned with overriding the OnEntry, OnExit, and OnException methods. Their purposes ought to be pretty straight forward. I also added a method to take the method&#8217;s parameter values and build it into a string like &#8220;\[param1 = A\]\[param2 = B\]&#8221;. The code for this looks like so:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using PostSharp.Laos;
using System.Collections;

[Serializable]
[System.Diagnostics.DebuggerStepThrough]
public class LoggableAttribute : OnMethodBoundaryAspect
{
    public override void OnEntry(MethodExecutionEventArgs event_args)
    {
        Logging.SetStartTime();
        Logging.SetExecutionTime(null);
        Logging.SetParameter("method_name", event_args.Method.Name);
        Logging.SetParameter("class_name", event_args.Instance.GetType().ToString());

        //for analysis, we want to be able to identify individual executions
        event_args.MethodExecutionTag = Guid.NewGuid();

        Logging.PushContext(event_args.MethodExecutionTag);
        Logging.SetParameter("parameters", ParametersToString(event_args));

        Logging.Info("Called " + event_args.Method);
    }

    public override void OnExit(MethodExecutionEventArgs event_args)
    {
        Logging.SetExecutionTime(DateTime.Now);
        Logging.SetParameter("parameters", ParametersToString(event_args));
        Logging.SetParameter("method_name", event_args.Method.Name);
        Logging.SetParameter("class_name", event_args.Instance.GetType().ToString());
        
        Logging.Info("Finished " + event_args.Method);

        Logging.PopContext();
    }

    public override void OnException(MethodExecutionEventArgs event_args)
    {
        Logging.SetExecutionTime(DateTime.Now);
        Logging.SetParameter("parameters", ParametersToString(event_args));
        Logging.SetParameter("method_name", event_args.Method.Name);
        Logging.SetParameter("class_name", event_args.Instance.GetType().ToString());
        
        Logging.Error("Error Encountered in " + event_args.Method, event_args.Exception);
    }

    //helpers
    private static String ParametersToString(MethodExecutionEventArgs event_args)
    {
        String output = "";
        if (event_args.GetReadOnlyArgumentArray() != null)
        {
            for (int i = 0; i < event_args.GetReadOnlyArgumentArray().Length; i++)
            {
                output += String.Format("[{0} = {1}]", event_args.Method.GetParameters()[i].Name, event_args.GetReadOnlyArgumentArray()[i]);
            }
        }
        return output;
    }
}
```

Pretty simple, considering what it does. Now that we&#8217;ve gone through all this effort to set things up, we can see where the magic happens. It seems like a lot of work, but this is where we get the payoff. Find a controller in your project, such as the default home controller. And just add your attribute to it:

```csharp
[HandleError]
[Loggable]
public class HomeController : Controller
{
    public ActionResult Index()
    {
        ViewData["Title"] = "Page Title";
        ViewData["Message"] = "Hey LessThanDotters!";

        return View();
    }
}
```

Now, the &#8220;[Loggable]&#8221; attribute is all that you need to add to any class that you want logging to take place on the three method boundaries that we wrote code for (there may be some limitations, but I&#8217;m not aware of them yet). You can add it on a method-by-method basis as well. If you want to stop logging a certain class/method, just remove the attribute. Its&#8217; really that easy.

I&#8217;m sure I will find some problems with this approach eventually (first try and all), and I will update with those as I find them. Or if anything is immediately apparent, please point it out in the comments!

Sorry this post was so heavy on the code, I just thought it was really cool and wanted to share. Hopefully it gives you your own ideas for ways to use the PostSharp library. 

Have fun!

 [1]: http://www.postsharp.org/
 [2]: http://wesshaddix.info/post/Logging-in-ASPNet-MVC-using-Log4Net-and-PostSharp.aspx
 [3]: http://logging.apache.org/log4net/release/sdk/log4net.Layout.PatternLayout.html