---
title: Monitoring and Logging as a Service – Reviews
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2012-07-05T13:43:00+00:00
ID: 1644
excerpt: There is a lot of value in knowing what the internals of your application are doing and, more importantly, knowing them 5 minutes ago before someone called to complain the system is slow. Instrumenting an application sounds like a complex task, but in my prior post I showed some sample code that allowed me to tie into several log services to store data from my system as it is running.
url: /index.php/enterprisedev/instrumentation/monitoring-and-logging-reviews/
views:
  - 25072
rp4wp_auto_linked:
  - 1
categories:
  - Instrumentation

---
There is a lot of value in knowing what the internals of your application are doing and, more importantly, knowing them 5 minutes ago before someone called to complain the system is slow. Instrumenting an application sounds like a complex task, but in [my prior post][1] I showed some sample code that allowed me to tie into several log services to store data from my system as it is running. 

Keep in mind, I have extremely high hopes for these types of services, due to my experience in instrumentation and logging in manufacturing environments ([see the first post][2]). None of these systems are built with this task specifically in mind, so services that are absolutely wonderful at their core competencies may not prove to be a good fit for this specific use case.

In any case, let's see what we're going to be looking at:

  * <a href="http://www.loggly.com/" title="Visit Loggly" target="_blank">Loggly</a> – "It's fast, fun and easy to use"
  * <a href="http://www.datadoghq.com/" title="Visit DataDog" target="_blank">DataDog</a> – "On a mission to bring sanity to IT Management"
  * <a href="https://www.splunkstorm.com/" title="Visit Splunk Storm" target="_blank">Splunk Storm</a> – "Your data has the answers, we help you find them."
  * <a href="http://www.sumologic.com/" title="Visit Sumologic" target="_blank">Sumo Logic</a> – "Make Your Applications Run Longer & Stronger"
  * <a href="http://www.logentries.com/" title="Visit logentries" target="_blank">logentries</a> – "We make your life easier"
  * <a href="https://papertrailapp.com/" title="Visit papertrail" target="_blank">papertrail</a> – "Get back to work."
A few of these were non-starters, but I'll record what I know about them. The rest I'll present in similar formats for comparison.

## Loggly

Loggly was one of the first logging services on the market as well as one of the first I instrumented with the application. 

### Highlights:

Loggly supports raw text, syslog, and JSON data posts and is a live service (not beta).

**Input Support:** HTTP with raw text or JSON plus an array of syslog options
  
**Ease of Setup:** No default inputs, have to create an initial HTTP JSON input which is fairly easy. Good samples provided for communicating with input.
  
**Searching:** The search uses a console-like command line input for searches with limited direction and assistance (more later), I didn't invest the time that would have been necessary to learn enough of the search interface to become even a beginning user
  
**Visualization:** Nonexistent. Has examples of how to build your own in python, jQuery, and google charts

### General Opinion:

I would not use this service for application instrumentation. The search is the only feature it offers and that is in beta (Loggly has been live for several years). The command-line search idea is cute, but to me is an example of form over function that suffers from sluggish response times, low usability, and an unnecessary initial learning curve. My use of the site was characterized by extremely long loading times (30-60 seconds) and the lack of initial defaults for new users seems like a missed opportunity. If they were to focus on fixing the performance issues, improving the usability, and implementing visualization, I'd be ready to give them a second look.

### Running the Sample Site w/ Loggly

Clone a copy of the sample application from [tarwn/InstrumentationSampleCode on github][3]. Go to the [Loggly.com website][4], sign up for an account (you can get a free account by dragging the bars on the pricing page to the lowest values), create an HTTP input with JSON. 

Once you have the input created, create or edit the _sensitive.config_ file at the root of the solution with the following values (substitute your GUID in where appropriate):

**sensitive.config**

```text
LogProvider = Loggly
Loggly.BaseURL = https://logs.loggly.com/inputs/put-your-guid-here
```
Open the site, try a few things, then switch over to the Loggly screen and see if a page will load long enough to show it received some data.

## DataDog (Could Not Review)

[DataDog][5] looked like it would be a good option, but after setting up my initial account I found I could not continue until I installed an agent on a server with a supported operating system. Unfortunately Windows is not supported (which was not obvious until you got signed up), so I was unable to continue. 

They did email me a few days later to ask if I had any difficulties getting signed up since I hadn't finished the activation step, and the email looked personal and friendly, so I may return and see if they can help me get past that step to try the service for instrumentation purposes. 

## Splunk Storm

<a href="https://www.splunkstorm.com/" title="Visit Splunk Storm" target="_blank">Splunk Storm</a> is a beta service from Splunk, which is a name that should be highly recognizable in the logging world. Storm is their beta service to offer the capabilities of Splunk without a local installation.

### Highlights: 

**Input Support:** HTTP with raw text, syslog, netcat, and "any other application that transmits via TCP or UDP"
  
**Ease of Setup:** Easy enough. API tokens are already available for use but error reporting on individual HTTP posts can sometimes be useless (501s for instance)
  
**Searching:** The search for splunk is the bar to measure the others by. They use a text-based language but everything on the screen is clickable, common fields are captured and listed on the side for easy searching, and the search language itself is something you can pick up a bit at a time
  
**Visualization:** Pretty decent but there are some definite learning curve moments and when you try to graph too much data it throws away parts of the dataset entirely

### General Opinion:

I have been using Splunk Storm for longer than any of the other services. The service itself is very promising. It offers a really good search capability with a great drilldown capability and setup was easy. Often it will give mini-help boxes when typing terms into the search and suggest similar searches I have done recently, details on commands, and suggestions for commands I may want to use.

Unfortunately they are using the bad definition of "beta", instead of the "it's relatively stable but not feature complete" definition. There have been at least two occurrences where I was locked out of the system while they performed maintenance, on one occasion an update actually locked me out of the main account for 4-5 days (with a constant friendly reminder that service would be back momentarily). Documentation links are present throughout the application but most of them lead to 404 pages. The fact that charts throw away portions of the dataset when you ask them to chart too much can be incredibly frustrating (when you figure out what it's doing). 

After the initial exposure I started to find that I was spending more and more time in the splunk documentation (once you figure out how to get there) simply trying to learn their command-line language because the interface only gets you so far. The power of the tool is impressive, but the level of effort I would have to spend learning how to use their tool is a common annoyance that reminds me of Loggly's command-line tool. I've also seen more of their 404 narwhal page then I ever expected to see, as apparently any page that is left open for too long refreshes and drops you there (session time out perhaps?).

### Running the Sample Site w/ Splunk Storm:

Clone a copy of the sample application from [tarwn/InstrumentationSampleCode on github][3]. Go to the <a href="http://www.splunkstorm.com" target="_blank">splunkstorm.com website</a> and sign up for an account (all accounts are free while it is in beta).

In the inputs section of the project there is a tab called "API" that has the access token and project id. Add or edit the _sensitive.config_ file at the root of the .Net solution, substituting your values where appropriate:

**sensitive.config**

```text
LogProvider = storm

Storm.AccessToken = your-access-token-here
Storm.BaseURL = https://api.splunkstorm.com/1/inputs/http
Storm.ProjectId = your-project-id-here
```
Load up the sample app, click some buttons, then switch over to Splunk Storm and try out the search. Clicking different items in the log entries will fire off searches. Unfortunately charting is almost a blog series on it's own, sorry.

## Sumo Logic (Could Not Review)

<a href="http://www.sumologic.com" title="Visit Sumo Logic" target="_blank">Sumo Logic</a> claims to provide "the first cloud-based log management and analytics solution". The demo video shows a product that has similar capabilities to Splunk in the search area with additions of automatic pattern detection and monitoring. Initially I did not demo the product because they don't offer unattended demos, you have to sign up and wait for someone to contact you to hold your hand while you demo their service. Later, as I was reading their FAQ, I found that they do not offer an API, accepting data only via collectors that are installed on your local system. The collector idea isn't bad, manufacturing historians have been doing this for a couple decades after all, but without demoing I couldn't tell how easy or hard it would be to install them and automate that installation for those of us with large numbers of servers or elastic infrastructure.

## logentries

<a href="http://logentries.com//" title="Visit logentries" target="_blank">logentries</a> is a cloud logging service that has been on the market for over a year and offers free and metered accounts.

### Highlights: 

**Input Support:** HTTP with raw text, agents, or an array of syslog options
  
**Ease of Setup:** No default inputs, have to create an initial log. Doesn't respond to success or error HTTP calls, making troubleshooting difficult. Initial data didn't show in site until I specifically hit the refresh button in the top right of the app, leading to several hours of frustrated and unnecessary troubleshooting initially.
  
**Searching:** The search seems fairly basic, but they pattern matching and tagging ability lends a second dimension to search capabilities. Creating patterns was not straightforward, however.
  
**Visualization:** Graphs are purely around tags and data records, no ability to extract values from entries and graph them (so somewhere between loggly and splunk in this regard)

### General Opinion:

Logentries doesn't seem like a good solution for application instrumentation, but this is more due to their focus being different from what I was looking for. I did have difficulties with the initial setup due to the lack of response from their HTTP service, the fact that all of their language examples use raw TCP sockets instead of HTTP libraries, and the delays in data being sent and actually showing up in the interface.

I also found the search and pattern sections frustrating. That being said, some of this was due to me trying to misuse their service for instrumentation and because their documentation is not linked from inside the interface. The search supports regular expressions and provides a good number of examples, which makes it almost as powerful as the other services without the need to learn a whole new syntax or language. 

With one exception, if I was looking for a log monitoring and alerting system I would consider starting here because I could get farther for a reduced learning curve and startup time. Unfortunately the exception is that I can't tell if my data is actually reaching them or not. Switching to the TCP method they outline may or may not resolve this, but the lack of error feedback even if I got it working with that method.

### Running the Sample Site w/ logentries:

Clone a copy of the sample application from [tarwn/InstrumentationSampleCode on github][3]. Go to the <a href="http://www.logentries.com" target="_blank">logentries.com website</a> and sign up for an account (there is a free account with limited storage).

Add a new Host entry, then inside that host add a new Log with a type of "API Library/HTML". Add or edit the _sensitive.config_ file at the root of the .Net solution, substituting your values where appropriate:

**sensitive.config**

```text
LogProvider = logentries

logentries.BaseURL = http://api.logentries.com
logentries.AccountKey = your-account-key-here
logentries.Host = your-host-name
logentries.Log = your-log-name-or-key
```
Note: I fired up the sample app pointed at logentries when I started writing this up, my data still hasn't shown up. I'm not sure if it will or not.

## Papertrail (Could Not Review)

<a href="https://papertrailapp.com/" title="Visit papertrail" target="_blank">Papertrail</a> looks like a pretty solid service, from their front page. Unfortunately while they do support a large number of systems as log inputs, a REST API is not one of them. They do support syslog as an input source, however, so it should be possible to communicate from an instrumented app over TCP using the syslog format.After digging into more of their documentation, however, it looks like this system could offer some instrumentation support from an alerting perspective, but doesn't really offer the types of capabilities we would want for instrumentation (aggregating and analyzing values in like log entries, for instance API executing times across multiple calls). 

## Summary

Most instrumentation data options require local installers, many based on RRD storage or other, similar methods. I did find an open source server called [Nimbits][6] which is designed as an operational process historian and would likely be very good at storing discrete counter and numeric values (similar to graphite or an RRD tool). I also found <a href="https://metrics.librato.com/" title="Visit Metrics at Librato" target=_"blank">Metrics</a>, which appears to be a much better fit for instrumentation then these logging services and is worth considering for a future instrumentation post. Unfortunately these options are a different enough model that I couldn't compare them side-by-side with the logging services in this post.

It is certainly possible to instrument your applications with limited impact and modification to the source application, however most of the logging services I could find don't offer the type of support you would want for that type of exercise. Splunk Storm seemed to offer the closest capabilities, but the service has the ability to be unavailable when you need it the most. That being said, many of the services would probably shine when used for the purpose they were created for and the fact that I couldn't misuse them for this other purpose shouldn't dissuade you for looking into them for log management and analytics purposes.

 [1]: /index.php/All/?p=1748 "Monitoring and Logging as a Service - The Common Bits"
 [2]: /index.php/All/?p=1747 "Monitoring and Logging as a Service - Introduction"
 [3]: https://github.com/tarwn/InstrumentationSampleCode "InstrumentationSampleCode on GitHub"
 [4]: http://www.loggly.com
 [5]: http://www.datadoghq.com/ "Visit DataDogHQ"
 [6]: http://www.nimbits.com/ "Visit nimbits page"