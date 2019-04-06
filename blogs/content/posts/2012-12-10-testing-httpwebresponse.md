---
title: Testing the Not-So-Testable HttpWebResponse
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2012-12-10T14:56:00+00:00
excerpt: Recently I was working on a library to consume a REST API without exposing any of the specifics to the rest of the application. Implementing a common interface and set of custom exceptions was easy enough, but exercising the internal logic was going to be tough.
url: /index.php/desktopdev/mstech/testing-httpwebresponse/
views:
  - 23028
rp4wp_auto_linked:
  - 1
categories:
  - 'C#'
  - Microsoft Technologies
tags:
  - mocking
  - unit testing

---
Recently I was working on a library to consume a REST API without exposing any of the specifics to the rest of the application. Implementing a common interface and set of custom exceptions was easy enough, but exercising the internal logic was going to be tough. 

While I could use the live REST API to verify the general logic worked, I had limited options when it came to the full range of HTTP responses and communication failures. Add in the impact those live API calls would have on my build process performance, the occasional failures of my build due the imperfections of talking to a live service, and the overhead of maintaining separation between my test and live data in that service&#8230;what I really had on my hands was the beginning of years of random, painful maintenance.

If only I could mock WebRequest and have it return carefully crafted responses to test my code with, all without ever touching the real network.

> [MSDN][1]: You should never directly create an instance of the HttpWebResponse class.

Hmm, ok, maybe not.

Despite having done this search before, this time around I uncovered a couple posts that helped me find a solution to this whole mess. Although not before I left some helpful feedback on the MSDN page about the difference between opinion and documentation.

Yep, making friends.

_The source code for this post is located on github, with the sample service implementation and test projects: [tarwn/TestableHttpWebResponse][2]_

## Let&#8217;s Start with the Tests

I&#8217;ve created a sample service implementation with two API call implementations. Each one builds a WebRequest, executes it, and analyzes the response. A retry policy wraps around the request execution, evaluating exceptions to determine whether to retry or map them to a local exception type to be rethrown.

Here is an example of one of those calls and the synchronous method it uses internally:

**TestableHttpWebResponse.Sample/SampleService.cs**

<pre>public class SampleService
{
	// ...

	public ServiceResponse ListRemoteStuff(string operation)
	{
		var uri = new Uri(_baseUri, operation);
		var request = WebRequest.Create(uri);
		request.Headers.Add("version", "123-awesome");
		return SendRequest(request);
	}
	
	// ...

	private ServiceResponse SendRequest(WebRequest request)
	{
		return _retryPolicy.ExecuteAction&lt;ServiceResponse&gt;(() =&gt;
		{
			try
			{
				var response = (HttpWebResponse)request.GetResponse();
				var reader = new StreamReader(response.GetResponseStream());
				var message = reader.ReadToEnd();
				return new ServiceResponse() { IsSuccess = true, Message = message };
			}
			catch (WebException we)
			{
				throw MappedException(we);
			}
		});
	}

	// ...
}</pre>

Testing a method like this typically requires an integration test against the live service. With the provided TestableHttpWebResponse and TestableWebRequest, however, we can set up an expected request and response and verify the service reacts appropriately.

**1: Register the TestableWebRequestCreateFactory**
  
WebRequest.Create(_uri_) uses a factory to produce the relevant WebRequest instance of a Uri, based on the prefix. So first things first, lets register a new prefix and a factory to serve up requests:

<pre>[TestFixtureSetUp]
public void TestFixtureSetup()
{
	WebRequest.RegisterPrefix("test", TestableWebRequestCreateFactory.GetFactory());
}</pre>

TestableWebRequestCreateFactory.GetFactory() exposes a singleton that can be referenced from any of the tests in this class. When the WebRequest object receives a Uri starting with &#8220;test://&#8221;, it will call the associated factory, giving us the opportunity to respond with a Request object of our choosing.

A common base URI will prove helpful as we write the tests:

<pre>public Uri BaseUri { get { return new Uri("test://mydomain.com/api/"); } }</pre>

**2: Building a Test**

The easiest test to start with is one that will test the &#8220;happy path&#8221; where our API call receives a 200 Success response. 

First we need to set up the request:

<pre>var operation = "ListOfStuff";
var expectedRequest = new TestableWebRequest(new Uri(BaseUri, operation));</pre>

Next we need to set up the response the request will return when it is executed:

<pre>expectedRequest.EnqueueResponse(HttpStatusCode.OK, "Success", "Even More Success", false);</pre>

And then add it to the Factory so it will be available when WebRequest calls it:

<pre>TestableWebRequestCreateFactory.GetFactory().AddRequest(expectedRequest);</pre>

Put all of this together and we have:

**TestableHttpWebResponse.Sample.Tests/SampleServiceTests.cs**

<pre>[Test]
public void ListRemoteStuff_ValidRequest_ReturnsSuccessfulResponse()
{
	var operation = "ListOfStuff";
	var expectedRequest = new TestableWebRequest(new Uri(BaseUri, operation));
	expectedRequest.EnqueueResponse(HttpStatusCode.OK, "Success", "Even More Success", false);
	TestableWebRequestCreateFactory.GetFactory().AddRequest(expectedRequest);
	var service = new SampleService(BaseUri);

	var response = service.ListRemoteStuff(operation);

	Assert.IsTrue(response.IsSuccess);
}</pre>

This exercises the entire successful path of the operation without any additional abstractions in our API code or reliance on external communications and services.

## Testing Http Status Codes

Another tricky part of testing a service is figuring out how to test HTTP codes other then the success case. 

The sample service maps received Protocol Errors (401, 404, etc) to a local exception so the code consuming this library doesn&#8217;t have to know how to parse WebExceptions. 

**TestableHttpWebResponse.Sample/SampleService.cs**

<pre>private Exception MappedException(WebException we)
{
	// map to custom exceptions
	if (we.Status == WebExceptionStatus.ProtocolError)
	{
		var reader = new StreamReader(we.Response.GetResponseStream());
		var message = reader.ReadToEnd();
		var httpResponse = (HttpWebResponse)we.Response;
		switch (httpResponse.StatusCode)
		{
			case HttpStatusCode.NotFound:
				if (httpResponse.StatusDescription.Contains("Dohicky"))
					return new DohickyNotFoundException(message, we);
				else
					return new GenericNotFoundException(message, we);
			default:
				return new ExampleOfAnotherUsefulException(message, we);
		}
	}
	else
		return new SampleServiceOutageException(we);
}</pre>

Exercising the mapping logic is going to require the WebRequest to receive a WebException. Let&#8217;s make that happen.

_Yes, I know a HEAD request will break this, that&#8217;s why it&#8217;s called &#8220;sample&#8221; code 🙂_

In the first test, we used the EnqueueResponse method of the TestableWebRequest to set up a 200 Success response. It&#8217;s just as simple to return a 404 Http code with the expected message and request body:

**TestableHttpWebResponse.Sample.Tests/SampleServiceTests.cs**

<pre>expectedRequest.EnqueueResponse(HttpStatusCode.NotFound, "Dohicky not found", "I couldn't find your dohicky because I don't like you", true);</pre>

Which allows us to create the ExpectedException test:

<pre>[Test]
[ExpectedException(typeof(DohickyNotFoundException))]
public void ListRemoteStuff_404DohickeyNotFound_ThrowsDohickeyNotFoundException()
{
	var operation = "ListOfStuff";
	var expectedRequest = new TestableWebRequest(new Uri(BaseUri, operation));
	expectedRequest.EnqueueResponse(HttpStatusCode.NotFound, "Dohicky not found", "I couldn't find your dohicky because I don't like you", true);
	TestableWebRequestCreateFactory.GetFactory().AddRequest(expectedRequest);
	var service = new SampleService(BaseUri);

	var response = service.ListRemoteStuff(operation);

	// expect exception
}</pre>

## Testing Other WebExceptions

What about connection failures? Well there is another version of the EnqueueResponse method that allows us to queue up an exception to be returned from the Request, like so:

<pre>expectedRequest.EnqueueResponse(new WebException("I'm broke!", WebExceptionStatus.ConnectFailure));</pre>

Just like the previous test, we can use that response to put together a full test

**TestableHttpWebResponse.Sample.Tests/SampleServiceTests.cs**

<pre>[Test]
[ExpectedException(typeof(SampleServiceOutageException))]
public void ListRemoteStuff_ServiceOutage_ThrowsSampleServiceOutage()
{
	var operation = "ListOfStuff";
	var expectedRequest = new TestableWebRequest(new Uri(BaseUri, operation));
	expectedRequest.EnqueueResponse(new WebException("I'm broke!", WebExceptionStatus.ConnectFailure));
	TestableWebRequestCreateFactory.GetFactory().AddRequest(expectedRequest);
	var service = new SampleService(BaseUri);

	var response = service.ListRemoteStuff(operation);

	// expect exception
}</pre>

## Testing the Retry Policy

Retry policies are trickier, in that they need to be able to execute a Request multiple times and receive new responses. By enqueueing (sp?) multiple responses on the request, we can exercise the retry policy:

**TestableHttpWebResponse.Sample.Tests/SampleServiceTests.cs**

<pre>[Test]
public void ListRemoteStuff_TimeoutOccurs_TruesASecondTime()
{
	var operation = "ListOfStuff";
	var expectedRequest = new TestableWebRequest(new Uri(BaseUri, operation));
	expectedRequest.EnqueueResponse(new TimeoutException("took too long, so sorry"))
				   .EnqueueResponse(HttpStatusCode.OK, "All Good", "Nothing to see, please move along", false);
	TestableWebRequestCreateFactory.GetFactory().AddRequest(expectedRequest);
	var service = new SampleService(BaseUri);

	var response = service.ListRemoteStuff(operation);

	Assert.AreEqual("Nothing to see, please move along", response.Message);
}</pre>

And there we have it, all the various flavors of an HttpWebRequest.

## The Testable Classes

The Testable classes are still under construction. At the time of this post they support the functionality above as well as the ability to set and verify Request Headers and write and verify the Request stream contents (upload). Currently the asynchronous methods (BeginGetResponse/EndGetResponse) are not implemented, but I&#8217;ll be adding those soon along with SampleService calls that exercise those via TPL and async/await logic. I&#8217;ll also be looking through WebRequest for other properties or methods I haven&#8217;t imlpemented yet to see what&#8217;s useful.

Hopefully others will find this useful as well.

 [1]: http://msdn.microsoft.com/en-us/library/system.net.httpwebresponse.aspx "HttpWebResponse on MSDN"
 [2]: https://github.com/tarwn/TestableHttpWebResponse "tarwn/TestableHttpWebResponse on github"