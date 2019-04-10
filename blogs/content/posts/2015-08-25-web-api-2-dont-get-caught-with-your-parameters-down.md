---
title: Web API 2 – don’t get caught with your parameters down
author: Tahir Khalid
type: post
date: 2015-08-26T02:51:31+00:00
ID: 4130
url: /index.php/webdev/web-api-2-dont-get-caught-with-your-parameters-down/
views:
  - 4890
rp4wp_auto_linked:
  - 1
categories:
  - ASP.NET
  - 'C#'
  - Microsoft Technologies
  - Server Programming
  - Web Developer
tags:
  - .net 4.5
  - 'c#'
  - web api 2
  - web development

---
Hi,

A quick post for anyone else who has been literally dying from a lack of sleep trying to workout something that should be fairly simple.

Quick background:  I got interested in Web API after I decided to build a friend a simple booking website driven by HTML5, jquery and originally an ASP.NET/c# .NET 4 Web Service.  Having tested it out I came across a CORS related issue and being very impatient I just could not be bothered to work around it (and lets face it, if you have to do that with CORS its a hack in my book, it just doesn't feel right in the dynamic world of Web API 2.x).

So I thought it was about time I looked at Web API proper and as 2.0 had recently surfaced it was a good a time as any to jump on board.

First things first I created a simple test app remembering my good friend Kevin's advice to me: “keep that \**** simple or I will come round and choke you out!” – no greater words spoken.  I followed this good tutorial to get me started: <a title="Getting Started with ASP.NET Web API 2 (C#)" href="http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api" target="_blank"><span style="color: #000000">Getting Started with ASP.NET Web API 2 </span></a><span style="color: #898989"><a title="Getting Started with ASP.NET Web API 2 (C#)" href="http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api" target="_blank">(C#)</a> </span>however what seems to be a common occurrence with me, shit just goes wrong!

So what gives? Well I kept getting the dreaded 404 error when I adapted the tutorial to my simple project which consists of the following:

  * HTML page that has a $.getJSON block which calls the uri (for this simple test I did which was to call a parameter less method, well I tried)
  * Web API 2 project consisting of: 
      * simple entity called TestMessage that has a couple of properties like name, messagetext
      * simple controller inheriting from ApiController and implementing an IHttpActionResult method called GetMessage()

So noting too fancy:

<pre>public class TestMessage
    {
        public string Name{ get; set; }
        public string MessageText { get; set; }
    }
</pre>

The controller class:

<pre>public IHttpActionResult GetMessage()
{
   return Ok("{}");
}
</pre>

The little gotcha above btw is that you need a return JSON object or null otherwise you will get a failed response at the client side, not the least of my worries though as I was getting another 404 message:

“No matching action method found in the selected controller”

Awesome, just what I was expecting!  I thought okay simple enough just a mismatch somewhere but nope wasn't anything I tried and I spent days scouring through Stack Overflow (and occasionally crying) and other online resources (part of the problem being I had no clue what my question was to begin with or rather the correct search terms, this came later after much much pain and vomiting).

Some bright spark suggested decorating the method but that completely breaks the new Web API pattern, just didn't seem right and felt like a major hack again so I went back to trying to workout how and why I was getting this 404 issue.  I even tried changing my client side call from $.getJSON to the old school $.Ajax making a GET call but still failed until I eventually came across a great post that set me on the right track (but wasn't quite the complete solution).

By the way at any point in your development hell you go through this, make sure you get a handle on how to debug Web API using resources like <a title="Get Firebug" href="http://getfirebug.com/" target="_blank">Firebug </a>(Chrome Debugger is good but the layout is too shitty for me and I hate Google anyway) and <a title="Fiddler" href="http://www.telerik.com/fiddler" target="_blank">Fiddler </a>(an awesome tool for composing JSON requests and tracking requests/responses for stuff like Web API).

First I came across this great article by a cool developer called Dave Ward (author of the Encosia website): “<a title="Using jQuery to POST [FromBody] parameters to Web API" href="http://encosia.com/using-jquery-to-post-frombody-parameters-to-web-api/" target="_blank">Using jQuery to POST [FromBody] parameters to Web API</a>” however when I followed the steps mentioned by Dave I was still getting some problems namely I was not able to post any kind of JSON data using the getJSON jquery method so what the hell was going on, again?!  Well the first part was resolved by using the [FromBody] as suggested by Dave but something else was still causing my request to turn to shit.

<span style="color: #999999"><em>Disclaimer: Shit is a universal word used by developers and has a neutral meaning neither an offensive or defensive word but cool all the same!</em></span>

Turns out the JSON data was being passed in as a string and therefore just being ignored and set to null and the solution lay here in this awesome Stack Overflow post: <a title="http://stackoverflow.com/a/29978090" href="http://stackoverflow.com/a/29978090" target="_blank">http://stackoverflow.com/a/29978090</a>

Yep everything is awesome now so the second part of the solution was to set the type of the incoming parameter to that of my entity i.e. the TestMessage class I pasted earlier in this post and then in my getJSON jquery method I did something like this:

<pre>function SendMessage() {
            $.getJSON("http://localhost/api/message/getmessage, { "name": "kermit", 
                      "messagetext": "hello, LTD!" })

                .done(function (data) {
                    $('#message').text("OK");
                })
                .fail(function (jqXHR, textStatus, err) {
                    $('#message').text('Error: ' + err);
                });
        }
</pre>

And hey presto all sorted.   What we did here was to create a complex parameter using a JSON format which is then passed through to the controller action GetMessage.  I expanded on this by making my action more complex using the excellent <a title="Newtonsoft Json.Net" href="http://www.newtonsoft.com/json" target="_blank">Newtonsoft JSON library</a> to assist with deserialising the data, inflating an entity and then building up an email message to send out using the <a title="System.Net.Mail Namespace" href="https://msdn.microsoft.com/en-us/library/system.net.mail(v=vs.110).aspx" target="_blank">System.Net.Mail</a> namespace.

Well that worked for me and I hope it works for you!