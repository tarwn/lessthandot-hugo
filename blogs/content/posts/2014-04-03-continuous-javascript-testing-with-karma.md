---
title: Continuous Javascript Testing with Karma
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2014-04-03T15:27:39+00:00
url: /index.php/webdev/uidevelopment/javascript/continuous-javascript-testing-with-karma/
views:
  - 12700
rp4wp_auto_linked:
  - 1
categories:
  - Javascript
  - Unit Testing
tags:
  - jasmine
  - javascript
  - karma
  - requirejs

---
I use a continuous testing tool named [NCrunch][1] for all of my .Net code. In fact, [NCrunch has spoiled me][2] so much that manually running tests is bordering on painful. I&#8217;ve gotten used to doing absolutely nothing and still having the latest build results, test results, code coverage, highlighted execution paths for failed tests, and little hover notices on each line that passed an exception. Make a change, magic happens. All coding should work like that.

The AngularJS team has built a continuous javascript testrunner named [karma][3], so of course I&#8217;m going to give it a try. 

At the time of this post, the current version is 0.12 and I will be using Jasmine 2.0 ([woo, Async!][4]), RequireJS (also a [recent topic][5]), and [Squire.js][6] (for injecting mocks). The sample project I&#8217;m using for demos is just something that was handy that already had some tests.

# Setting up Karma

Setting up karma is pretty straightforward. The karma site has clear information already on how to [install the package][7] and [set up the configuration][8], so I&#8217;m not going to go into the details on that.

One minor variance is that I chose to install karma local to my project rather than globally, so I&#8217;ll have to run the tools from the node_modules subdirectory.

I created a package.json file for my project:

**package.json:** [townthing/package.json][9]

<pre>{
	"name": "townthing",
	"version": "0.1.0",
	"description": "sample project I'm playing with",
	"repository": "https://github.com/tarwn/townthing",

	"devDependencies": {
		"karma": "~0.11",
		"karma-jasmine": "~0.2",
		"karma-phantomjs-launcher": "~0.1",
		"karma-chrome-launcher": "~0.1"
	}
}</pre>

<div style="background-color: #eeeeee; padding: .5em;">
  <b>Important Note:</b> Be careful with your versions. I&#8217;ve found out the hard way that karma keeps their dependencies wide open &#8220;*&#8221; until they are ready to move versions, then they lock them down to something that may not actually be the latest version. Karma 0.10 worked fine with karma-jasmine 0.2 until they released 0.10.10 which locked in a requirement for karma-jasmine ~0.1. More recently the karma-phantomjs-launcher has revved to 1.3, which somehow broke a perfectly working 0.12 karma against 1.2 despite there being no actual code changes (I suspect a versioning side-effect mixed with their *-version acceptance).
</div>

And then go through the steps to create my karma configuration:
  
**karma.conf.js:** [townthing/karma.conf.js][10]

<pre>module.exports = function(config) {
	config.set({
		basePath: 'town/js',
		frameworks: ['jasmine', 'requirejs'],
		files: [
		  'test/test-main.js',
		  {pattern: '**/*.js', included: false}
		],
		exclude: [ '**/main.js' ],
		reporters: ['dots'],
		port: 9876,
		colors: true,
		// possible values: config.LOG_DISABLE || config.LOG_ERROR || config.LOG_WARN || config.LOG_INFO || config.LOG_DEBUG
		logLevel: config.LOG_DEBUG,
		autoWatch: true,
		browsers: ['PhantomJS'],
		captureTimeout: 60000,
		singleRun: false
	});
};</pre>

I already had a set of 68 specs configured to run from my SpecRunner file, with my Require.js configuration specified inline. Before I co-opted this project as a blog example, the tests were specified in script tags, but I have moved them to a require() statement and used the custom boot script created for my [Jasmine 2.0 and RequireJS post][11].

**SpecRunner:** [townthing/js/test/SpecRunner.json][12]

<pre><!DOCTYPE HTML&gt;
<html&gt;
<head&gt;
	<meta http-equiv="Content-Type" content="text/html; charset=UTF-8"&gt;
	<title&gt;Jasmine Spec Runner v2.0.0</title&gt;

	<link rel="shortcut icon" type="image/png" href="../lib/jasmine-2.0.0/jasmine_favicon.png"&gt;
	<link rel="stylesheet" type="text/css" href="../lib/jasmine-2.0.0/jasmine.css"&gt;

	<script type="text/javascript" src="../lib/jasmine-2.0.0/jasmine.js"&gt;</script&gt;
	<script type="text/javascript" src="../lib/jasmine-2.0.0/jasmine-html.js"&gt;</script&gt;
	<script type="text/javascript" src="../lib/jasmine-2.0.0/boot-without-onload.js"&gt;</script&gt;

	<script src="../lib/require-2.1.11.js"&gt;</script&gt;

	<script type="text/javascript"&gt;
	require.config({
		baseUrl: "../src",
		paths: {
			"knockout": "../lib/knockout-3.0.0",
			"Squire": "../lib/Squire"
		}
	});

	require(['../test/compass.spec', '../test/tile.spec', '../test/tree.spec', '../test/weather.spec'],function(){
		window.executeTests();
	});
	</script&gt;
</head&gt;
<body&gt;
</body&gt;
</html&gt;</pre>

The folder structure is a little odd, as this was originally just a play project. My test libraries are mixed with the core libraries and my specs and src have a flat structure. Were this a production project, I would also try to find a way to combine this inline config with the one below and generate the list of spec files instead of hand-coding them.

Because I am using RequireJS, I&#8217;ve included that option in my configuration and created a RequireJS configuration based on the one supplied in the [RequireJS instructions][13] on the karma site.

**test-main.js:** [townthing/town/js/test/test-main.js]()

<pre>var tests = [];
for (var file in window.__karma__.files) {
	if (window.__karma__.files.hasOwnProperty(file)) {
		if (/spec\.js$/.test(file)) {
			tests.push(file);
		}
	}
}

requirejs.config({
    // Karma serves files from '/base'
    baseUrl: '/base/src',

	paths: {
		"knockout": "../lib/knockout-3.0.0",
		"Squire": "../lib/Squire"
	}
});
require(tests, function(){
	window.__karma__.start();
});</pre>

The biggest difference between my script and the sample one is I am loading the tests and starting karma after the configuration, rather than inside it. I am using Squire to mock several of the RequireJS modules for tests, had I used the configuration to start karma then each time I created a new instal of Squire I would have kicked off conflicting runs when it ran the same configuration.

Running karma locally is then as easy as: `node .\node_modules\karma\bin\karma start karma.conf.js`

It&#8217;s only a few more steps to create a single test-main.js that both the jasmine SpecRunner file and karma can share.

# The Results

Once I have the configurations set up, my tests run successfully from karma. I have them configured to use PhantomJS, but can also override that by sending in command-line arguments to do a single-run in other browsers (like Chrome) when I need to troubleshoot.

`node .\node_modules\karma\bin\karma start karma.conf.js --single-run`

<div id="attachment_2492" style="width: 577px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2014/03/karma.png"><img src="/wp-content/uploads/2014/03/karma.png" alt="Successful Tests w/ Non-Impacting Errors" width="567" class="size-full wp-image-2492" srcset="/wp-content/uploads/2014/03/karma.png 567w, /wp-content/uploads/2014/03/karma-300x113.png 300w" sizes="(max-width: 567px) 100vw, 567px" /></a>
  
  <p class="wp-caption-text">
    Successful Tests w/ Non-Impacting Errors
  </p>
</div>

I was getting errors about missing timestamps when Squire loads some of the dependencies, but the files are found so I&#8217;m not sure why they are occurring (and they don&#8217;t happen on one of my other projects). I found a similar [issue][14] and [stackoverflow][15] question, so I&#8217;m not the only one with this particular issue. 

`node .\node_modules\karma\bin\karma start karma.conf.js --single-run`

<div id="attachment_2543" style="width: 815px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2014/04/karma2.png"><img src="/wp-content/uploads/2014/04/karma2.png" alt="Karma Run - Take 2 - (Node hasn&#039;t eaten my blue background in this window)" width="805" height="88" class="size-full wp-image-2543" srcset="/wp-content/uploads/2014/04/karma2.png 805w, /wp-content/uploads/2014/04/karma2-300x32.png 300w" sizes="(max-width: 805px) 100vw, 805px" /></a>
  
  <p class="wp-caption-text">
    Karma Run &#8211; Take 2 &#8211; (Node hasn&#8217;t eaten my blue background in this window)
  </p>
</div>

I was able to correct the issue from switching my test-main.js require basePath from &#8220;base/src&#8221; to &#8220;/base/src&#8221;, I&#8217;m still digging into why this worked.

# My Thoughts

NCrunch set the bar high, and while karma runs my tests continuously, I think comparing it NCrunch would be unfair to karma because it just isn&#8217;t in the same league.

Running locally, Karma does not give me that much more value than just refreshing a SpecRunner file in the browser. With the browser I have to change Alt+Tab to the window and F5 refresh, with karma the console output of test results is there, but it doesn&#8217;t have the browser&#8217;s ability to click on an error and see the code in context, see files that didn&#8217;t load correctly, etc. Karma has a plugin infrastructure for other reporters, but the few I&#8217;ve looked at have been focused on providing static files. I briefly looked at an HTML reporter in the hope that it might do some AJAX-y magic, but it simply created HTML output files.

One thing I really like about karma is it&#8217;s ability to easily plug in other browsers and run across one or more simultaneously. In a build server environment, this would mean I could easily run my JS unit tests across a wide set of browsers, collect the results, and then either capture the text output from karma or use a plugin for my build server to integrate in the results.

So overall, I think it makes a great tool for running unit tests the same locally and on the build server and being able to easily do so across a wide range of browsers, but I really don&#8217;t like the choice of using the console as the primary output. I think they overlooked the fact that they already have a browser front-end and a web server that could have been used to provide a richer front-end (potentially one that could be compared to NCrunch) and stil had a slimmer console or other-plugin-of-choice reporting mechanism for those that prefer it or are automating against it.

 [1]: http://www.ncrunch.net/
 [2]: /index.php/enterprisedev/unittest/reducing-code-build-test-friction/
 [3]: http://karma-runner.github.io/
 [4]: /index.php/webdev/uidevelopment/javascript/testing-asynchronous-javascript-w-jasmine/ "Testing Asynchronous Javascript w/ Jasmine 2.0.0"
 [5]: /index.php/webdev/uidevelopment/javascript/unit-testing-with-jasmine-2-0-and-require-js/ "Unit Testing with Jasmine 2.0 and Require.JS"
 [6]: https://github.com/iammerrick/Squire.js/ "iammerrick/Squire.js on github"
 [7]: http://karma-runner.github.io/0.12/intro/installation.html "Karma - Installation"
 [8]: http://karma-runner.github.io/0.10/intro/configuration.html "Karma - Configuration"
 [9]: https://github.com/tarwn/townthing/blob/master/package.json
 [10]: https://github.com/tarwn/townthing/blob/master/karma.conf.js
 [11]: /index.php/webdev/uidevelopment/javascript/unit-testing-with-jasmine-2-0-and-require-js/
 [12]: https://github.com/tarwn/townthing/blob/54f182bf96ff036a8765f421884d465d890c598c/town/js/test/SpecRunner.html
 [13]: http://karma-runner.github.io/0.10/plus/requirejs.html "Karma - RequireJS"
 [14]: https://github.com/princed/karma-chai-plugins/issues/4
 [15]: http://stackoverflow.com/questions/20733090/karma-error-there-is-no-timestamp-for