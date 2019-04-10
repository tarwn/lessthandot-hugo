---
title: Application Event Log for your website
author: pmch22
type: post
date: 2008-12-08T10:17:20+00:00
ID: 240
url: /index.php/webdev/webdesigngraphicsstyling/application-event-log-for-your-website/
views:
  - 8213
rp4wp_auto_linked:
  - 1
categories:
  - Web Design, Graphics and Styling

---
A healthy application is one which does not throw up any application errors. However in unexpected situations when it happens, error gets logged on the web server’s event log. It’s usually takes a little while to scroll through the event viewer to find your error. One good option is to create a separate application error log for your website. 

Let’s see some code now. Start by importing the System.Diagnostics namespace.
  
Server.GetLastError() is accessible only at two places – 1. Page\_Error event handler of a page . 2. Application\_Error event handler in the global.asax 

In this article, we’ll trap the unhandled errors in Application_Error event.

```csharp
<%Import Namespace= “System.Diagnostics”%>

void Application_Error(object sender, EventArgs e) 
    { 
       //Code that runs when an unhandled error occurs
      string LogName = "MySiteAppLog";
      string SourceName = "MySiteAppSource";

      //get the HTTPContxt object for the current request.  
        HttpContext ctx = HttpContext.Current;
        Exception ex = ctx.Server.GetLastError().GetBaseException();
   
        string errorInfo = "<br>Offending URL: " + ctx.Request.Url.ToString();
        errorInfo += "<br>Exception Details: " + ex.Message.ToString();

       // ctx.Server.ClearError();

	//Creates the application log when the first error occurs.
        if (!(EventLog.SourceExists(SourceName)))
        {
            EventLog.CreateEventSource(SourceName, LogName);
        }
        EventLog MyLog = new EventLog();
        MyLog.Source = SourceName;

        MyLog.WriteEntry(errorInfo, EventLogEntryType.Error);
         
        Session.Abandon();

    }
```
Note in the above code, Server.ClearError() is commented. This brings the customErrors section of the web.config into play. Once the error occurs, the error is logged into the event log and then redirected to a custom error page. Therefore make sure the customErrors section of the web.config file has an entry like this

```csharp
<customErrors mode= “On” defaultRedirect= “AppError.htm”></customErrors>
```

Continuing with the creation of Eventlog, create a sample page on your site to generate application error and then follow the below steps.

1.Open Regedt32. 

2.Go to HKEY\_LOCAL\_MACHINESystemCurrentControlSetServicesEventLog. 

3.Add FULL permissions to ASPNET and Network services account. 

4.Browse to the sample page and create application error.

5.This error should be logged in Eventlog as a new log – MyAppLog. 

6.Once the log is created remove permissions to NetworkServices account in Event log. Network services account is needed only to create the log. 

7.Repeat error again on the sample page. A new error should be logged in the event log. 

8.Repeat from remote system(to test as non- admin user). 

9.Check event log. 

The only drawback to create to an application specific event log is that you need admin rights on the web server. If that’s not possible, you can send out an email or write to a log file on the server. Code to send email –

```csharp
<%Import System.Web.Mail%>
 MailMessage  errMail = new MailMessage();
errMail.To= "ITgroup@company.com";
errMail.From= "web@company.com"; 
errMail.Subject = "Application error on website";
errMail.Body = errorInfo;
SmtpMail.SmtpServer = "Mailserverip";
SmtpMai.Send(errMail);
```
Having an application specific event log helps you to view all the errors in one shot and saves you the time of browsing the regular event log and searching errors for your site.