---
title: Sharing ASP.net Session State Between Applications with SQL Server – Part II
author: Alex Ullrich
type: post
date: 2009-01-24T19:45:00+00:00
ID: 289
excerpt: |
  In Part I we set up our awesome website, and a nice SQL Server database to manage our sessions.  But we're not done yet, now we want to share that session state between our site and another web application, in this case a service.
  
  So, now that our si&hellip;
url: /index.php/webdev/serverprogramming/aspnet/sharing-asp-net-session-state-between-ap/
views:
  - 25538
rp4wp_auto_linked:
  - 1
categories:
  - ASP.NET

---
In [Part I][1] we set up our awesome website, and a nice SQL Server database to manage our sessions. But we&#8217;re not done yet, now we want to share that session state between our site and another web application, in this case a service.

So, now that our site is good to go, we can set up the webservice call. First, we need to set up the service. For this example we&#8217;ll just return a simple string, from the session of course.

```csharp
[WebMethod(EnableSession = true)]
public String RetrieveValue()
{
    return Session["sessionEntry"].ToString();
}
```

I know, its&#8217; breathtaking 😉

Its&#8217; important to note the &#8220;EnableSession&#8221; attribute here, this is what give your service access to the Session.

Another thing you will need to do is set your webservice to allow POSTs, like so:

```xml
<add verb="GET,HEAD, POST" path="ScriptResource.axd" type="System.Web.Handlers.ScriptResourceHandler, System.Web.Extensions, Version=3.5.0.0, Culture=neutral, PublicKeyToken=31BF3856AD364E35" validate="false"/>
```

(this is found in the &#8220;httpHandlers&#8221; section of the web.config)

To be able to call the webservice, we need its&#8217; IP from the debugger, so set the service to be your startup project and fire it up. Make a note of the address (for example, http://localhost:52588) because we will need it here. May as well just leave the services running on localhost so we can call them (I haven&#8217;t figured out a way to make ctrl+f5 start up both).

To call the webservice we&#8217;ll use a bit of jquery goodness. Its&#8217; easy since we aren&#8217;t returning a custom type. I can assure you this technique works for those as well, as long as they can be serialized &#8211; i&#8217;ll address this later on because there are some issues with sharing types on the server side that I would like to get into at some point.

Here&#8217;s the javascript to replace the stub we set up in part I:

```javascript
<script type="text/javascript">
    function retrieve() {
        $.ajax({
            type: 'POST',
            dataType: 'xml',
            url: 'http://localhost:52588/Service1.asmx/RetrieveValue',
            success: function(data) {
                $('#retrievedValue').get(0).value = $('string', data).text();
            },
            error: function(data) {
                alert('AJAX Error!')
            }
        });
    }
</script>
```

You will need to replace the URL with whatever the VS debugger has assigned to your service (or the URL of an actual service even!).

Now, when you try to run this (make sure that you start the webservice project first!), you will get an error. The reason for this is that, despite the fact that you have both apps pointing at the same database for session state, they each maintain their own independent session. So we need to trick the database into thinking both applications are one and the same. Luckily we can do this with a simple change to a stored proc in the ASPState database, and the connection strings in our web.config files 

In our connection string, we just need to add the Application Name property. So our web.config entry becomes: 

```xml
<sessionState mode="SQLServer" sqlConnectionString="Data Source=127.0.0.1; Integrated Security=SSPI; Application Name=TEST" cookieless="false" timeout="20"/>
```

So how can we use this then? Lets check out the stored procedure change (found at [sneal.net][2]). There&#8217;s a proc called &#8220;TempGetAppID&#8221; in the ASP state database. I don&#8217;t know all the inner workings of this database, but its used within the database to identify the application connecting to it. So we can alter the proc to include the kind Mr. (or Mrs.) Sneal&#8217;s change:

sql
-- start change

    -- Use the application name specified in the connection for the appname if specified
    -- This allows us to share session between sites just by making sure they have the
    -- the same application name in the connection string.
    DECLARE @connStrAppName nvarchar(50)
    SET @connStrAppName = APP_NAME()

    -- .NET SQLClient Data Provider is the default application name for .NET apps
    IF (@connStrAppName <> '.NET SQLClient Data Provider')
        SET @appName = @connStrAppName

    -- end change
```

So now, the database managing session state should treat both your website and your webservice as parts of the same application, and you will be able to retrieve the value you stashed in the session through javascript. Its&#8217; clearly not a very good examplle, but I am sure you can think of better ways to use this new power 🙂

If you&#8217;d like to download the source code for this project, you can do so [here][3]

Got a question on ASP.net? Check out our [ASP.net Forum][4]!

 [1]: /index.php/WebDev/ServerProgramming/ASPNET/sharing-asp-net-session-state-between-we
 [2]: http://www.sneal.net/blog/2007/06/27/SharingSessionBetweenWebAppsViaConfiguration.aspx
 [3]: /wp-content/uploads/blogs/WebDev//SessionTest.zip ""
 [4]: http://forum.ltd.local/viewforum.php?f=27