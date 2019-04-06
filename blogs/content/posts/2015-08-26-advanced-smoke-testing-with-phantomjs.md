---
title: Advanced Smoke Testing with PhantomJS
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2015-08-26T12:16:24+00:00
url: /index.php/webdev/advanced-smoke-testing-with-phantomjs/
views:
  - 6025
rp4wp_auto_linked:
  - 1
categories:
  - Web Developer
tags:
  - automated testing

---
Recently I found myself wanting a new kind of safety net. There are any number of surprise problems that can show up in front-end development, from mistyped image URLs to bad output when the minification script barfs to the unexpected surprises after adding new dependencies. As an application scales from smaller to larger, it becomes even more time consuming to check all of the interfaces and look for little things like 404s, script errors, and odd side effects. 

While manual testing is possible, we&#8217;re only human and will eventually miss something. Plus there&#8217;s the ongoing cost of doing all that testing. Enter the computer, better suited at repetitive tasks in a fraction of the time. 

So here are the things we want to test:

  * No 404s
  * No Script Errors
  * Load time (and comparison to a standard expectation)
  * File count (and comparison&#8230;)
  * File count (and comparison&#8230;)

And here are the hard parts:

  * Most of the pages I want to test are behind logins
  * Some have load delays
  * All have logic that runs after DOMReady to render for the user
  * It has to run on my local dev environment as well as against a deployed environment

And lastly, I want to touch enough of the files to warm up static file caches and kick off any run-time compilation that needs to take place.

This script is a work in progress, so far it seems reusable and handles script error detection and 404 detection. It supports adhoc assertions against the page as well, for verifying elements are present or the title.

# Getting Started

It was pretty clear I needed to be able to perform actions against a browser, evaluate the state of elements, and test assertions against both the state and resources that were loaded. [Selenium/WebDriver][1] provides some of the capabilities I need, but not all of them. Another option would be a proxy between a Selenium implementation and the web server, but this seems like I&#8217;m layering on extra complexity. After looking at several headless browsers, I decided [PhantomJS][2] had the closest set of features I needed.

I started with a script to test the LessThanDot.com login process. This gives me a lot of the basics I need, allowing me to come back later to add the more complex logic I will need for a modular SPA with delayed loading animations and so on. Knowing the fragility that occurs any time you write code directly against a browser page, I chose to separate my test script from the browser control logic from the page definitions. I based my abstractions on the [PageObject][3] pattern to define and maintain the individual pages separately from the control logic and the test steps.

[<img src="/wp-content/uploads/2015/08/AdvancedSmokeTest_00.png" alt="AdvancedSmokeTest_00" width="411" height="267" class="aligncenter size-full wp-image-4090" srcset="/wp-content/uploads/2015/08/AdvancedSmokeTest_00.png 411w, /wp-content/uploads/2015/08/AdvancedSmokeTest_00-300x194.png 300w" sizes="(max-width: 411px) 100vw, 411px" />][4]

The Test Script(s) describe the steps we want to take as we browse the site and verifies the browsing experience matches what we were expecting. The BrowserController manages the page lifecycle as a page is loaded, translating it into the matching page object that our test will interact with. The custom pages extend the Basic Page object, adding in logic to find specific element son the page, like a login button or username input.

# Test Script

This test script is designed to verify the following path:

  1. Open http://ltd.local
  2. Verify the title is correct and that we aren&#8217;t logged in yet
  3. Click the &#8220;login&#8221; link in the nav bar
  4. Verify we&#8217;re on the login page
  5. Enter my username and password, then click the &#8220;Login&#8221; button
  6. Get the success message
  7. Verify the page then redirects back to where I started, http://lesssthandot.com

Writing this script uncovered several peculiarities in the site title and URLs that I hadn&#8217;t noticed before and would have fixed if I had had this tool when we were initially building it.

[/AdvancedSmokeTest/test.js][5]

<pre>// ... load dependencies ...

// ... capture args for username, password, etc ...

// setup controller
var logger = new BasicLogger(1);
var controller = new BrowserController('./pages', './browser', logger);

// ... add a bunch of google addresses to URLs and erors we get ...

// run test
Promise.resolve().then(function(){

	logger.stdout('Step 1', 'Load the site, we won\'t be logged in');
	return controller.goToUrl('http://ltd.local');
}).then(function(pageObject){
	assert.equal(pageObject.getUrl(), 'http://ltd.local/');
	assert.equal(pageObject.getTitle(), 'Less Than Dot - Launchpad - Less Than Dot');
	assert.ok(pageObject.getIsLoggedOut(), 'Logged out on initial visit');

	logger.stdout('Step 2', 'Navigate to Login Page from menu');
	return pageObject.pressLogin();
}).then(function(pageObject){
	assert.equal(pageObject.getUrl(), 'http://ltd.local/login.php?backtrack=http://ltd.local/index.php?');
	assert.equal(pageObject.getTitle(), 'Less Than Dot - Launchpad - Less Than Dot - Login');

	logger.stdout('Step 3', 'Perform login');
	pageObject.typeUsername(config.username);
	pageObject.typePassword(config.password);
	return pageObject.clickLoginButton();
}).then(function(pageObject){
	assert.equal(pageObject.getUrl(), 'http://ltd.local/login.php?backtrack=http://ltd.local/index.php?');
	assert.ok(pageObject.getIsLoggedIn(), 'Logged in now');

	logger.stdout('Step 4', 'Wait for automatic redirect');
	return pageObject.waitForRedirectTo('http://ltd.local');
}).then(function(pageObject){
	assert.equal(pageObject.getUrl(), 'http://ltd.local/index.php?');
	assert.equal(pageObject.getTitle(), 'Less Than Dot - Launchpad - Less Than Dot');

	logger.stdout('Success', 'We have logged in successfully.');
	controller.phantomPage.render('success.png');
}).catch(function(err){
	if(err.name == 'AssertionError'){
		logger.error('ASSERT FAIL', err.message);
	}
	else if(err.message){
		logger.error('Test', 'unhandled error: ' + err.name + ':' + err.message + '\nStack Trace:\n' + err.stack);
	}
	else{
		logger.error('Test', 'unhandled error: ' + err);
	}
	controller.phantomPage.render('lasterror.png');
}).finally(function(){
	phantom.exit();
});</pre>

The script mostly follows the list from above. I use a Promise library ([bluebird][6]) for asynchronous actions (load this website and let me know when it&#8217;s ready). When each action returns, I make assertions about what the page state is supposed to be, with the mechanics of how I do things like figuring out our login status or how to type a username value into the right box hidden inside the page object. If those assertions fail, they are thrown as errors and the script skips to the catch statement at the end to report the failure.

In the error state, I take a screenshot to help debug. In the success, I take a screenshot to later us for either version comparison or a slideshow.

The logger object replaced console.log so I could fine tune the level of information going to the console and put some easily parsed codes on the lines as well:

Example Output:

<pre>[OUT] [Step 1         ] Load the site, we won't be logged in
[---] [goToUrl        ] http://ltd.local
[---] [onUrlChanged   ] Going to http://ltd.local/
[---] [onLoadFinished ] Page "http://ltd.local/" loaded with status success
[---] [setLoaded      ] Page loaded in 2125ms :: http://ltd.local/
[OUT] [Step 2         ] Navigate to Login Page from menu
[---] [onUrlChanged   ] Going to http://ltd.local/login.php?backtrack=http://ltd.local/index.php?
[---] [onLoadFinished ] Page "http://ltd.local/login.php?backtrack=http://ltd.local/index.php?" loaded with status success
[---] [setLoaded      ] Page loaded in 965ms :: http://ltd.local/login.php?backtrack=http://ltd.local/index.php?
[OUT] [Step 3         ] Perform login
[---] [onUrlChanged   ] Going to http://ltd.local/login.php?backtrack=http://ltd.local/index.php?
[---] [onLoadFinished ] Page "http://ltd.local/login.php?backtrack=http://ltd.local/index.php?" loaded with status success
[---] [setLoaded      ] Page loaded in 984ms :: http://ltd.local/login.php?backtrack=http://ltd.local/index.php?
[OUT] [Step 4         ] Wait for automatic redirect
[---] [onUrlChanged   ] Going to http://ltd.local/index.php?
[---] [onLoadFinished ] Page "http://ltd.local/index.php?" loaded with status success
[---] [setLoaded      ] Page loaded in 4206ms :: http://ltd.local/index.php?
[OUT] [Success        ] We have logged in successfully.</pre>

# BrowserController

The BrowserController wraps around the PhantomJS page events and pushes the appropriate values into a loaded pageObject and/op handles errors. Script and resource errors are surfaced as &#8220;reject&#8221; calls (which are then handled by the catch back in the test). The onUrlChanged event followed by an onLoadFinished event allows the BrowserController to know a page has been loaded so it can compose additional page behavior logic onto the base page, passing it back to the test. It also has the ability to tie into events that will help track the number and size of files, and potentially even checks that specific files were or were not included (bundles versus individual scripts, for instance).

<div style="padding: .5em; margin: .5em; background-color: #eeeeee;">
  <b>Ask Me About PhantomJS and GZip</b><br />I later found out that size is not going to happen. Phantom doesn&#8217;t&#8217;t handle/expose gzip or chunked file size properly even when supplied in the Response headers. &#8220;Disable gzip&#8221; was a common &#8220;fix&#8221; that totally ignores the fact that the only reason to use Phantom is to validate your site and turning off gzip means you&#8217;re validating it completely unrealistically (since you probably had it turned on for a reason).
</div>

Moving on&#8230;0&#8230;

[/AdvancedSmokeTest/browser/browserController.js][7]

<pre>function BrowserController(pageDir, browserControllerDir, logger){
	var self = this;

	// ... some setup ...

	self.preloadPagesDefinitions = function(){
		// .. pre-load the page object definitions we provide the path too above ...
	};

	self.goToUrl = function(url){
		self.logger.debug(1,'goToUrl', url);

		return self.loadNewPage(function(){
			self.phantomPage.open(url);
		});
	};

	self.loadNewPage = function(navigationAction){
		return new Promise(function(resolve, reject){
			self.preloadPagesDefinitions();

			var newUrlCalledYet = false;

			// setup to capture error conditions
			self.phantomPage.onResourceTimeout = function(request) {
				// ...
				reject('Page timed out');
			};

			self.phantomPage.onResourceError = function(resourceError) {
				if(self.isIgnorableError(resourceError.url)){
					// ...
					return;
				}

				// ...
				reject('Page load error');
			};
	
			setTimeout(function(){
				if(!currentPage.isLoaded){
					reject('After 15 seconds I gave up');
				}
			}, 15000);
			
			self.isIgnorableError = function(msg){
				return _.some(self.ignorableErrors, function(ignorableError){
					return msg.match(ignorableError);
				});
			};

			self.phantomPage.onError = function(msg, trace){
				if(self.isIgnorableError(msg)){
					// ...
					return;
				}

				var traceContent = // ...

				// ...
				reject('Browser script error occurred.\nMessage: ' + msg + '\nTrace:\n' + traceContent.join('\n'));
			};

			// setup to capture when page load finishes
			self.phantomPage.onLoadFinished = function(status){
				if(newUrlCalledYet)
				{
					try{
						// ...
						currentPage.setLoaded(status);

						if(status === "success")
							resolve(currentPage);
					}
					catch(err){
						reject(err);
					}
				}
			};

			// track navigation and information events

			// ... more events ...
			
			self.phantomPage.onUrlChanged = function(targetUrl) {
				self.logger.debug(1, 'onUrlChanged', 'Going to ' + targetUrl);
				newUrlCalledYet = true;
			};

			// execute navigation action
			var currentPage = new BasicPage(self.phantomPage, logger);
			navigationAction();

		}).then(function(currentPage){
			// attach pageutils
			return pageUtils.initializeUtils(self.phantomPage, browserControllerDir).then(function(feedback){
				return currentPage;
			});

		}).then(function(currentPage){
			var url = self.phantomPage.url;
			_.forEach(knownPages, function(knownPage){
				if(url.match(knownPage.pattern)){
					knownPage.attachBehavior(currentPage, self.phantomPage, self.loadNewPage);
				}
			});
			return currentPage;
		}).then(function(currentPage){
			return currentPage;
		});
	};

}</pre>

The main work for the BrowserController is near the end. We pass in a navigation action to perform that we know will be asynchronous, after wiring up all of the events it needs to watch it then executes that action and waits for the response to finish (the first then). This is triggered by the onLoadFinished event being called after the page has finished loading, which calls the resolve() method. We then attach some additional page utilities (jQuery if it isn&#8217;t present, an autotype plugin) and then scan through the list of known pages we preloaded at the top and add the behavior of each one that matches to the basicPage we started with.

Along the way, we also have hooks into other properties, like timeouts and resource load errors, which will call the reject() method instead of resolve. This causes a break in the script, skipping ahead to the catch in the outer test script.

# A PageObject

The PageObjects are pretty simple. They have a match function to help compare against a URL and some functions that abstract interactions with the browser as a simple function we can call from our tests. This ensures that if we change around the screens or make changes to elements we care about, we only have to update our page abstraction and not track down ID or CSS magic strings throughout the test code.

Rather than make my page objects match one-to-one to a browser page, I have chosen to compose the behavior from multiple pages. So for this test I have an anyPage that supports any page in the LessThanDot website, and I have the loginPage which is the specific behavior you would only find on the login page. When the login page is loaded, the BrowserController will attach the behavior from both of these pages, reducing the need to duplicate the logic in &#8220;anyPage&#8221; in every page (assuming I have more of them, which I will for my real test case).

[/AdvancedSmokeTest/pages/anyPage.js][8]

<pre>var pageUtils = require("../browser/pageUtils");

module.exports = {
	name: "anyPage",
	description: "common behavior for all lessthandot pages",
	pattern: /.*/,
	attachBehavior: function(basicPageObject, phantomPage, loadNewPage){

		basicPageObject.getIsLoggedIn = function(){
			return pageUtils.getElement(phantomPage, '#snav a:contains("Logout")').getIsVisible();
		};

		basicPageObject.getIsLoggedOut = function(){
			return pageUtils.getElement(phantomPage, '#snav a:contains("Login")').getIsVisible();
		};

		basicPageObject.getLogInWelcomeText = function(){
			return pageUtils.getElement(phantomPage, '#snav').getInnerText();
		};

		basicPageObject.pressLogin = function(){
			return loadNewPage(function(){
				pageUtils.getElement(phantomPage, '#snav a:contains("Login")').click();
			});
		};

		basicPageObject.waitForRedirectTo = function(url){
			return loadNewPage(function(){
				// just wait patiently...
			});
			// and then we could then an assertion with a URL if we had assertions...
		};
	}
};</pre>

You can see this is a pretty small file and it wouldn&#8217;t be hard to define multiple of these pages to support a larger number of tests. The pageUtils library provides the ability to get DOM elements that have been wrapped with helper functions for visibility, click interaction, and even typing values. We expose abstractions that are simple enough to describe to someone over the phone (are you logged in? what does the welcome text say? Press the login button) and wire this to the lower-level language the browser&#8217;s JavaScript engine understands.

# But that&#8217;s not everything&#8230;

This provides a base I need to start implementing smoke tests against my target front-end application. My next steps will be to layer in logic to track the files being downloaded and build statistics around timing and quantity. I currently output a log message for the page load time, but would like to expose this as a property instead so we could assert against it. I also want to add some masking capabilities to the output to mask things like the password values I have passed in so they don&#8217;t get preserved in a build server log somewhere.

 [1]: /index.php/webdev/uidevelopment/automated-web-testing-with-selenium-2/ "Less Than Dot: Automated Web Testing With Selenium 2"
 [2]: http://phantomjs.org/
 [3]: /index.php/webdev/uidevelopment/automated-web-testing-with-selenium-2/
 [4]: /wp-content/uploads/2015/08/AdvancedSmokeTest_00.png
 [5]: https://github.com/tarwn/Blog_PhantomExperiments/blob/master/AdvancedSmokeTest/test.js
 [6]: https://github.com/petkaantonov/bluebird/blob/master/API.md
 [7]: https://github.com/tarwn/Blog_PhantomExperiments/blob/master/AdvancedSmokeTest/browser/browserController.js
 [8]: https://github.com/tarwn/Blog_PhantomExperiments/blob/master/AdvancedSmokeTest/pages/anyPage.js