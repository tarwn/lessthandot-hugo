---
title: Unit Testing with Jasmine 2.0 and Require.JS
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2014-03-04T13:41:12+00:00
url: /index.php/webdev/uidevelopment/javascript/unit-testing-with-jasmine-2-0-and-require-js/
views:
  - 19862
rp4wp_auto_linked:
  - 1
categories:
  - Javascript
  - Unit Testing
tags:
  - jasmine
  - javascript
  - requirejs

---
Jasmine 2.0 has changed how it loads and executes tests, using a boot script now to handle the details. If you try to plug some require() calls into the sample SpecRunner.html page, Jasmine will be done and finished before the require() statement loads the test modules and their dependencies.

The problem is that RequireJS loads the dependencies asynchronously, but the standard boot script for Jasmine runs when window.onload is called. So how do we fix it?

## Option 1: Call window.onload Ourselves

One option to solve this is to simply call window.onload again:

<pre>&lt;script type="text/javascript" src="tests/lib/jasmine-2.0.0/jasmine.js"&gt;&lt;/script&gt;
&lt;script type="text/javascript" src="tests/lib/jasmine-2.0.0/jasmine-html.js"&gt;&lt;/script&gt;
&lt;script type="text/javascript" src="tests/lib/jasmine-2.0.0/boot.js"&gt;&lt;/script&gt;
&lt;script type="text/javascript" src="lib/require-2.1.8.min.js" data-main="test-main"&gt;&lt;/script&gt;

&lt;script type="text/javascript"&gt;
	// list spec files here
	require([
		"spec/someAwesomeProcess.spec",
		"spec/anotherAwesomeProcess.spec"

	], function () {
		window.onload();
	});
&lt;/script&gt;</pre>

But that&#8217;s icky and causes you to have two test bars across the screen (and probably doesn&#8217;t work well with other reporters either).

[<img src="http://blogs.ltd.local/wp-content/uploads/2014/02/JasmineDoubleFail.png" alt="JasmineDoubleFail" width="726" height="182" class="aligncenter size-full wp-image-2477" srcset="http://blogs.ltd.local/wp-content/uploads/2014/02/JasmineDoubleFail.png 726w, http://blogs.ltd.local/wp-content/uploads/2014/02/JasmineDoubleFail-300x75.png 300w" sizes="(max-width: 726px) 100vw, 726px" />][1]

Yeah, that&#8217;s special.

## Option 2: Custom Boot Script

Or we can fix the root cause, the fact that the tests are running on window.onload and that doesn&#8217;t play well with AMD. The boot script included with Jasmine is supposed to be a template that can be customized to your own needs, so let&#8217;s take advantage of that. Copying the existing boot script, we can replace the section that registers the tests to onload with one that will add a callable method to the window:

**boot-without-onload.js**

<pre>/**
   * ## Execution
   *
   * No onload, only on demand now
   */

  window.executeTests = function(){
    htmlReporter.initialize();
    env.execute();
  };</pre>

And then update our SpecRunner to include this replacement boot script and require the test files prior to executing the tests:

<pre>&lt;script type="text/javascript" src="tests/lib/jasmine-2.0.0/jasmine.js"&gt;&lt;/script&gt;
    &lt;script type="text/javascript" src="tests/lib/jasmine-2.0.0/jasmine-html.js"&gt;&lt;/script&gt;
    &lt;script type="text/javascript" src="tests/lib/jasmine-2.0.0/boot-without-onload.js"&gt;&lt;/script&gt;
    &lt;script type="text/javascript" src="lib/require-2.1.8.min.js" data-main="test-main"&gt;&lt;/script&gt;

    &lt;script type="text/javascript"&gt;
        // list spec files here
        require([
            "spec/someAwesomeProcess.spec",
            "spec/anotherAwesomeProcess.spec"

        ], function () {
            window.executeTests();
        });
    &lt;/script&gt;</pre>

And there we go, Jasmine is now working exactly the same as if we were running without RequireJS (and had pasted 500 script tags in the file).

 [1]: http://blogs.ltd.local/wp-content/uploads/2014/02/JasmineDoubleFail.png