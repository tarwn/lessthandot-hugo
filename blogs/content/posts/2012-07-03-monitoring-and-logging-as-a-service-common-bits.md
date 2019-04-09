---
title: Monitoring and Logging as a Service – The Common Bits
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2012-07-03T10:01:00+00:00
ID: 1643
excerpt: In my previous post I outlined some of my own history with monitoring and my intent to review several available logging services. To help compare apples to apples, the logging mechanisms and log messages will operate consistently for each of the selected services.
url: /index.php/enterprisedev/instrumentation/monitoring-and-logging-as-a-service-common-bits/
views:
  - 4572
rp4wp_auto_linked:
  - 1
categories:
  - Instrumentation

---
In my <a href="/index.php/EnterpriseDev/instrumentation/monitoring-and-logging-as-a-service" title="Monitoring and Logging as a Service - Introduction" target="_blank">previous post</a> I outlined some of my own history with monitoring and my intent to review several available logging services. To help compare apples to apples, the logging mechanisms and log messages will operate consistently for each of the selected services. 

The sample code is available on github and will continue to be updated as I add services I am trying: <a href="https://github.com/tarwn/InstrumentationSampleCode" title="InstrumentationSampleCode on GitHub" target="_blank">tarwn/InstrumentationSampleCode</a>

The purpose of this post is to outline that common portion of the application before we get into the reviews, so we have a common starting place and something to refer back to once we start customizing to support the services.

## The Sample Code

The sample code for this series is relatively simple. The solution includes 4 projects: Logging, LoggingTests, SampleSiteWithLogging, and SensitiveSettings.

### Logging

This is the core project for the post series. The goal of the Logging library is to make it extremely easy to log from inside the application (think statsd + graphite ala <a href="http://codeascraft.etsy.com/2011/02/15/measure-anything-measure-everything/" title="Measure Anything, Measure Everything post at Etsy" target="_blank">Etsy</a> simple) while making it easy to swap out which logging service we are using. 

There are two major components in the library, the Logger class which provides singleton access to the selected logging service and the required one line log calls and the HttpJsonPost, which handles HTTP requests (currently focused on key/value data for unstructured JSON or raw text posts).

The singleton can be setup with any implementation of an ILogProvider. It provides methods to log a single message or produce a message that automatically logs itself on disposal (capturing elapsed time and including it in the message). 

**Logging.Logger** (<a href="https://github.com/tarwn/InstrumentationSampleCode/blob/master/Logging/Logger.cs" title="Logger.cs on Github" target="_blank">source</a>)

```csharp
public class Logger {

	private ILogProvider _logProvider;
	private static Logger _current;

	public Logger(ILogProvider logProvider) {
		_logProvider = logProvider;
	}

	public void LogMessage(Dictionary<string, string> message, Action<Result> callback) {
		_logProvider.Log(message, callback);
	}

	public static void SetDefaultLogger(ILogProvider logProvider) {
		_current = new Logger(logProvider);
	}

	private static Logger GetDefaultLogger() {
		if (_current != null) {
			return _current;
		}
		else {
			throw new Exception("Default logger not setup");
		}
	}

	public static void Log(Dictionary<string, string> message, Action<Result> callback) {
		GetDefaultLogger().LogMessage(message, callback);
	}

	public static LoggerWithElapsedTime CaptureElapsedTime(Dictionary<System.String, System.String> message, Action<Result> callback) {
		return new LoggerWithElapsedTime(GetDefaultLogger(), message, callback);
	}
}
```
Each log call passes in a message and a callback that will be called with the result of the HTTP post, though so far the sample website code is using fire-and-forget, not bothering to provide a callback method.

To support the ILogProvider implementation, I created an [HttpJsonPost class][1] class and associated helpers to handle the HTTP communications. This class can do synchronous and asynchronous HTTP requests to the relevant services:

**Logging.Communications.HttpJsonPost** (<a href="https://github.com/tarwn/InstrumentationSampleCode/blob/master/Logging/Communications/HttpJsonPost.cs" title="HttpJsonPost on Github" target="_blank">source</a>)

```csharp
public class HttpJsonPost {

	Dictionary<string,string> _message;
	NetworkCredential _credentials;
	bool _useJson;

	public HttpJsonPost(Dictionary<string, string> message, NetworkCredential credentials = null, bool useJson = true) { /* ... */ }

	private HttpWebRequest InitializeRequest(string url, string method) { /* ... */ }

	public void Send(string url, string method, Action<Result> callback, bool processResponse = true) { /* ... */ }

	public void SendAsync(string url, string method, Action<Result> callback, bool processResponse = true) { /* ... */ }

	private void GetRequestStream(IAsyncResult result) { /* ... */ }

	private void GetResponseStream(IAsyncResult result) { /* ... */ }

	private void ProcessResponse(Func<WebResponse> getResponse, Action<Result> callback) { /* ... */ }

	private void WriteMessage(Stream stream) { /* ... */ }
}
```
This little bit of code and their supporting classes are all it takes to talk to the external logging services, and to do so with limited impact by using asynchronous requests and callbacks.

## The Sample Website

The sample website contains very little code that I added. The majority of the code is the “Empty MVC 3 Web Site” template from visual studio, I've added an extremely basic HomeController and some setup code in the global.asax to setup the log provider and log a few events.

**SampleSiteWithLogging.Global** (<a href="https://github.com/tarwn/InstrumentationSampleCode/blob/master/SampleSiteWithLogging/Global.asax.cs" title="Global.asax.cs on GitHub" target="_blank">source</a>)

```csharp
public class MvcApplication : System.Web.HttpApplication {
	
	/* ... */

	protected void Application_Start() {
		/* ... */

		ILogProvider provider = GetProviderFromSettings();
		Logger.SetDefaultLogger(provider);
		Logger.Log(new Dictionary<string, string>() { { "Type", "ApplicationStartup" }, { "Time", DateTime.UtcNow.ToString() } }, null);
	}

	protected void Application_BeginRequest() {
		Logger.Log(new Dictionary<string, string>() { { "Type", "ApplicationRequest" }, { "UserAgent", Request.UserAgent }, { "Time", DateTime.UtcNow.ToString() } }, null);
	}

	protected void Application_Error(Object sender, System.EventArgs e) {
		System.Web.HttpContext context = HttpContext.Current;
		System.Exception exc = Context.Server.GetLastError();

		Logger.Log(new Dictionary<string, string>() { { "Type", "ApplicationError" }, { "Exception", exc.ToString() }, { "Time", DateTime.UtcNow.ToString() } }, null);
	}

	protected ILogProvider GetProviderFromSettings() {
		SensitiveSettings.SettingsManager.LoadFrom(System.IO.Path.Combine(System.AppDomain.CurrentDomain.BaseDirectory, @"bin"));
		string provider = "null";
		if (SensitiveSettings.SettingsManager.Settings.ContainsKey("LogProvider")) {
			provider = SensitiveSettings.SettingsManager.Settings["LogProvider"];
		}

		switch (provider.ToUpper()) { 
			case "LOGGLY":
				return new LogglyProvider(SensitiveSettings.SettingsManager.Settings["Loggly.BaseURL"]);
			case "STORM":
				return new StormProvider(SensitiveSettings.SettingsManager.Settings["Storm.BaseURL"], SensitiveSettings.SettingsManager.Settings["Storm.AccessToken"], SensitiveSettings.SettingsManager.Settings["Storm.ProjectId"]);
			case "LOGENTRIES":
				return new LogentriesProvider(SensitiveSettings.SettingsManager.Settings["logentries.BaseURL"], SensitiveSettings.SettingsManager.Settings["logentries.AccountKey"], SensitiveSettings.SettingsManager.Settings["logentries.Host"], SensitiveSettings.SettingsManager.Settings["logentries.Log"]);
			default:
				return new NullLogProvider();
		}
		
	}
}
```
The global.asax file allows us to wire logic into the global application workflow. On startup we get a provider, based on our “Sensitive Settings” configuration file, set that as our default logger, then go ahead and log our first message with entries to indicate this is application startup and the current UTC timestamp. Each time we receive a request from a web browser, we use our on line logging call to log the browsers UserAgent string and a timestamp. When an error goes unhandled, we can log that too.

Elsewhere in our application we can use those same one line calls to pass information to the logging service. The HomeController logs information, but instead uses the CaptureElapsedTime method to log information and the time that elapses between it's instantiation and disposal.

**SampleSiteWithLogging.Controllers.HomeController** (<a href="https://github.com/tarwn/InstrumentationSampleCode/blob/master/SampleSiteWithLogging/Controllers/HomeController.cs" title="HomeController.cs on Github" target="_blank">source</a>)

```csharp
public class HomeController : Controller {
	public ActionResult Index() {
		return View();
	}

	public ActionResult ShortOperation() {
		using (var log = Logging.Logger.CaptureElapsedTime(new Dictionary<string, string> { { "Type", "SiteHit" }, { "Area", "HomeController" }, { "Method", "ShortOperation" } }, null)) {
			return View("Index");
		}
	}

	public ActionResult LongOperation() {
		using (var log = Logging.Logger.CaptureElapsedTime(new Dictionary<string, string> { { "Type", "SiteHit" }, { "Area", "HomeController" }, { "Method", "LongOperation" } }, null)) {
			Thread.Sleep((int)(3000 * new Random().NextDouble()));
			return View("Index");
		}
	}

}
```
These examples are actually more wordy than I would like. If I were building this as part of a production application, I would refactor them down to take explicit arguments or infer some of the values, reducing the size of each call even further. 

## “Sensitive Settings”

The Sensitive Settings library is just a quick settings library I threw together so I could prevent my API keys for these services from getting committed to github. The library is referenced by the website and unit test projects, each of which have a prebuild step to copy the “sensitive.config” file from the solution folder to their build target folders.

## LoggingTests

The LoggingTests folder is really just a way for me to easily test small chunks of the libraries without firing up the interface. These aren't real unit tests and that should serve as a second reason not to download and attempt to use the Logging library in a production application. 🙂

## Onward to the Reviews

Now that we have some code we can easily connect to all of the services, the next post will cover the services I tried and how they measured up against my expectations for this type of usage.

 [1]: https://github.com/tarwn/InstrumentationSampleCode/blob/master/Logging/Communications/HttpJsonPost.cs "HttpJsonPost on Github"