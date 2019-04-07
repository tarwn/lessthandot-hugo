---
title: Testing Asynchronous Javascript w/ Jasmine 2.0.0
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2014-01-07T17:00:00+00:00
ID: 2217
excerpt: 'Whether you have asynchronous methods in your client-side Javascript, are integration testing against an API, or are using an asynchronous module loader like RequireJS, asynchronous operations need testing too. Jasmine has become my framework of choice&hellip;'
url: /index.php/webdev/uidevelopment/javascript/testing-asynchronous-javascript-w-jasmine/
views:
  - 38528
rp4wp_auto_linked:
  - 1
categories:
  - Javascript

---
Whether you have asynchronous methods in your client-side Javascript, are integration testing against an API, or are using an asynchronous module loader like [RequireJS][1], asynchronous operations need testing too. Jasmine has become my framework of choice for client-side Javascript and I was happy to see the asynchronous syntax get some updates in the 2.0 release.

Release Announcement: [Jasmine is 5 and 2.0][2]
  
Jasmine Links: http://jasmine.github.io/

(Google and Bing will both provide the address to the 1.3.1 page if you search for jasmine, the link above is a landing page for both versions.)

<div style="background-color: #eeeeee; padding: 1em; text-align: center; margin-bottom: 1em">
  You can view or download the full code for the examples from <a href="https://github.com/tarwn/Blog_Jasmine2Async" title="tarwn/Blog_Jasmine2Async repo on github">github</a>
</div>

## Async in Jasmine 1.3.1

Prior to the 2.0 release, Jasmine used a pair of functions for latching to ensure the test fully executed before exiting. The latching method provides a runs() function for executing potentially asynchronous code and a waitsFor() function that polls a provided latch condition until it returns true (ready to continue). This works, but visually adds a bunch of noise to the test.

Here is what it looks like when we write a test against asynchronous code that isn&#8217;t waiting for asynchronous operations:

```javascript
it("won't be detected. It's executed without waiting for the asynchronous result", function(){
	var service = new sample.SampleService();
	
	var promise = service.getStuff(false);
	
	promise.then(function(message){
		expect(message).toBeDefined();
	}, function(error){
		expect("first test received error: " + error).toFail();
	});
});
```
When we break something in our code, instead of catching it in this test, the test will complete without waiting for the asynchronous execution and final evaluation. A bit later if we are still running other tests the error will pop up against a completely different test. This is problematic and will be a pain in the butt to figure out (and also shows some oddness in the jasmine architecture that a failure would be attached to a later test).

Using the latch method available in jasmine 1.3.1, we can add the runs() and waitsFor() calls to ensure this evaluates at the proper time:

```javascript
it("will be detected. It uses the runs() and waitsFor latch to wait for the async result", function(){
	var service = new sample.SampleService();

	var done = false;
	runs(function(){
		var promise = service.getStuff(false);
		
		promise.then(function(message){
			expect(message).toBeDefined();
		}, function(error){
			expect("second test received error: " + error).toFail();
		})
		.always(function(){
			done = true;
		});
	});
	
	waitsFor(function(){
		return done;
	});
});
```
Now the test will wait until we set our &#8220;done&#8221; variable to true (or 5000ms, the default timeout that I didn&#8217;t override on the waitsFor() call). As you can see, though, this also added some extra noise to the test, both in terms of extra characters to visually parse and extra function layers. It&#8217;s also quite a bit different then the pattern mocha follows, and if you&#8217;re going back and forth between mocha and jasmine you&#8217;ll have that moment where you have to shift mental gears.

This is was an excellent time to add [jasmine.async][3]. Jasmine.async introduces an AsyncSpec object with beforeEach(), it(), and afterEach() calls with a &#8220;done&#8221; wait handle parameter:

```javascript
var async = new AsyncSpec(this);
async.it("will be detected. It uses the jasmine.async library to wait for the result", function(done){
	var service = new sample.SampleService();
	
	var promise = service.getStuff(false);
	
	promise.then(function(message){
		expect(message).toBeDefined();
	}, function(error){
		expect("first test received error: " + error).toFail();
	})
	.always(done);
});
```
This looks very much like the initial, flawed version as we would implement it in mocha. The only change to the test logic is the single always() call at the end that will execute the &#8220;done&#8221; wait handle that the async.it() call provided. The test logic is still just as clean as the original at the cost of a single additional script include.

## Async in Jasmine 2.0.0

Jasmine 2.0.0 has updated the [async syntax][4] to work similar to Mocha right out of the box. The beforeEach(), it(), and afterEach() methods now take an optional parameter just like the wait handle parameter in Mocha and the jasmine.async library above.

Now it&#8217;s an even easier path from a flawed test like this:

```javascript
it("won't be detected. It's executed without waiting for the asynchronous result", function(){
		var service = new sample.SampleService();
		
		var promise = service.getStuff(false);
		
		promise.then(function(message){
				expect(message).toBeDefined();
		}, function(error){
				expect("first test received error: " + error).toFail();
		});
});
```
To a test that will correctly catch the asynchronous error, like this:

```javascript
it("will be detected. It uses the new jasmine async syntax", function(done){
	var service = new sample.SampleService();
	
	var promise = service.getStuff(false);
	
	promise.then(function(message){
		expect(message).toBeDefined();
	}, function(error){
		expect("second test received error: " + error).toFail();
	})
	.always(done);
});
```
Like the jasmine.async test in the 1.3.1 examples, the changes are minimal and don&#8217;t impact the flow of actually reading the test. 

## Upgrade right now!

Or not. Projects I&#8217;m starting from scratch are definitely getting 2.0.0. If you have an existing project on 1.3.1, there&#8217;s going to be some other inconsistencies between that and 2.0.0 (it is a major version upgrade, after all), but I think the improvements are worth at least taking it for a test drive.

 [1]: http://requirejs.org/
 [2]: http://pivotallabs.com/jasmine-2-release/ "2.0 release announcement at Pivotal Labs"
 [3]: https://github.com/derickbailey/jasmine.async "jasmine.async on github"
 [4]: http://jasmine.github.io/2.0/introduction.html#section-Asynchronous_Support "Asynchronous support in jasmine 2.0.0"