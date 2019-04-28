---
title: 'SignalR, Quartz.Net and ASP.Net: part 2 the webclient'
author: Christiaan Baes (chrissie1)
type: post
date: 2012-11-19T10:55:00+00:00
ID: 1794
excerpt: Some more SignalR, Quartz.Net and Asp.Net. Now with a webclient.
url: /index.php/webdev/serverprogramming/signalr-quartz-net-and-asp-1/
views:
  - 5379
categories:
  - ASP.NET
  - Server Programming
tags:
  - quartz.net
  - signalr

---
## Introduction

In my [previous post][1] I made a SignalR server with Quartz.net and a consoleclient.

Now it was time to make a webclient. And that took just a bit longer than expected.

## The client

First of all we [go and read the documentation][2] on how to do this.

I wish I would follow my advice from time to time.

Then I added an index.html page to my server code (and VS2012 really hated me for that).

then we add our code.

```html
&lt;!DOCTYPE html&gt;
&lt;html xmlns="http://www.w3.org/1999/xhtml"&gt;
&lt;head&gt;
    &lt;title&gt;NewCases in Becare&lt;/title&gt;
&lt;/head&gt;
&lt;body&gt;
    &lt;h2 id="title"&gt;Waiting for messages&lt;/h2&gt;
  
    &lt;p id="datetime"&gt;&lt;/p&gt;
    &lt;p id="numberofcases"&gt;&lt;/p&gt;
    &lt;ul id="messages"&gt;
    &lt;/ul&gt;
    &lt;script src="Scripts/jquery-1.6.4.min.js" type="text/javascript"&gt;&lt;/script&gt;
    &lt;script src="Scripts/jquery.signalR-1.0.0-alpha2.min.js" type="text/javascript"&gt;&lt;/script&gt;
    &lt;script src="signalr/hubs" type="text/javascript"&gt;&lt;/script&gt;
    &lt;script type="text/javascript"&gt;
        $(function () {
            var connection = $.hubConnection('http://localhost/');
            var proxy = connection.createHubProxy('beCare');

            proxy.on('newCases', function (data) {
                var d = new Date();
                var time = d.toLocaleString();
                $('#title').text('New cases');
                $('#datetime').text('Last updated: ' + time);
                $('#numberofcases').text('Number of cases found: ' + data.length);
                $('#messages').empty();
                $.each(data, function (i, val) { $('#messages').append('&lt;li&gt;' + val + '&lt;/li&gt;'); });
            });

            connection.start();
        });
    &lt;/script&gt;
    
&lt;/body&gt;
&lt;/html&gt;
```
Some gotchas. I had to give the url for the hubconnection to work. You have to be carefull with all the different syntaxes you might find all over the internet, they just loo similar. For instance <code class="codespan">$.connection.start();</code> does not work but might just work in other cases. You can even have a [different syntax][3] that should work with hubs too. But for some reason I never got that to work. 

The above works just fine with the server I made earlier. Well it does if you are testing with chrome. It did not work if I asked my users to tests it on their browsers which is by default IE in all shapes and sizes. The error you get is.

> No JSON parser found. Please ensure json2.js is referenced before the SignalR.js file if you need to support clients without native JSON parsing support, e.g. IE<8.

It might say <code class="codespan">&lt;ie8</code> but it didn&#8217;t work on my IE9 either. 

And of course there is a [FAQ for that][4].

> Why doesn&#8217;t SignalR work in browser IE6/IE7?
> 
> SignalR requires a JSON parser and ability to send xhr requests (for long polling). If your browser has none, you&#8217;ll need to include json2.js in your application (SignalR will throw an error telling your you need it as well). You can get it on NuGet.

But I did not read that before implementing this. Oops.

So you nuget json2 and add this line before the signalr script tag.

<code class="codespan">&lt;script src="Scripts/json2.min.js" type="text/javascript"&gt;&lt;/script&gt;</code>

## Conclusion

I should really read the docs before doing this kind of stuff&#8230;. nah, it&#8217;s much more fun this way.

 [1]: /index.php/WebDev/ServerProgramming/signalr-quartz-net-and-asp
 [2]: https://github.com/SignalR/SignalR/wiki/SignalR-JS-Client-Hubs-(No-Proxy)
 [3]: https://github.com/SignalR/SignalR/wiki/SignalR-JS-Client-Hubs
 [4]: https://github.com/SignalR/SignalR/wiki/Faq